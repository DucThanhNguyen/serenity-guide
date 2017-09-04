# Removing Tenant Dropdown From User Form

After you rebuild, and launch, now user page will be like this:

![Tenant2 Logged In](img/tenant2_filtered.png)

Yes, he can't see admin user anymore, but something is wrong. When you click *tenant2*, nothing will happen and you'll get an error *"Can't load script data: Lookup.Administration.Tenant*" in browser console:

This error is not related to our recent filtering at repository level. It can't load this lookup script, because current user has no permission to *Tenants* table. But how did he see it last time (in one case)? 

He could see it, because we first logged in as *admin* and when we open edit dialog for user, we loaded this lookup script. Browser cached it, so when we logged in with *tenant2* and open edit dialog, it loaded tenants from browser cache. 

But this time, as we rebuild project, browser tried to load it from server, and we got this error, as *tenant2* doesn't have this permission. It's ok, we don't want him to have this permission, but how to avoid this error?

We need to remove *Tenant* field from the user form. But we need that field for *admin* user, so we can't simply delete it from *UserForm.cs*. Thus, we need to do it conditionally.

Build the project, transform all templates and add method below to UserDialog.ts:

```ts
protected getPropertyItems() {
    var items = super.getPropertyItems();
    if (!Q.Authorization.hasPermission("Administration:Tenants"))
        items = items.filter(x => x.name != UserRow.Fields.TenantId);
    return items;
}
```

Dialogs gets list of fields it will show in its form by getPropertyItems method, which in turn loads them from server side form definition.

Here we exclude TenantId field, if current user doesn't have the tenants permission.

This doesn't modify the original user form, it just changes list for this dialog instance.

User _tenant2_ can now open the user dialog.