# Attributes

## Visible Attribute

> **namespace**: *Serenity.ComponentModel*, **assembly**: *Serenity.Core*

Controls visibility of a column or form field.

It is also possible to hide a field by passing *false* as its value, but [Hidden] attribute is recommended.

```cs
public class SomeColumns
{
    [Visible]
    public string ExplicitlyVisible { get; set; }
    [Visible(false)]
    public string ExplicitlyHidden { get; set; }
}
```

* User might still show the column by using the column picker if any.

## Hidden Attribute

> **namespace**: *Serenity.ComponentModel*, **assembly**: *Serenity.Core*

Hides a column or form field.

This is just a subclass of *VisibleAttribute* with *false* value.

```cs
public class SomeColumns
{
    [Hidden]
    public string HiddenColumn { get; set; }
}
```

* User might still show the column by using the column picker if any.

## HideOnInsert Attribute

> **namespace**: *Serenity.ComponentModel*, **assembly**: *Serenity.Core*

Controls whether a field is visible on new record mode.

* This only works with forms, not columns.

```cs
public class SomeColumns
{
    [HideOnInsert]
    public string HideMeOnInsert { get; set; }
    [HideOnInsert(false)]
    public string DontHideMeOnInsert { get; set; }
}
```

## HideOnUpdate Attribute

> **namespace**: *Serenity.ComponentModel*, **assembly**: *Serenity.Core*

Controls whether a field is visible on edit record mode.

* This only works with forms, not columns.

```cs
public class SomeColumns
{
    [HideOnUpdate]
    public string HideMeOnUpdate { get; set; }
    [HideOnUpdate(false)]
    public string DontHideMeOnUpdate { get; set; }
}
```

## Insertable Attribute

> **namespace**: *Serenity.ComponentModel*, **assembly**: *Serenity.Core*

Controls if a property is editable in new record mode.

* When used on row fields, turns on or off the Insertable flag.

* It has no effect on columns

```cs
public class SomeForm
{
    [Insertable(false)]
    public string ReadOnlyOnInsert { get; set; }
}
```

## Updatable Attribute

> **namespace**: *Serenity.ComponentModel*, **assembly**: *Serenity.Core*

Controls if a property is editable in edit record mode.

* When used on row fields, turns on or off the Updatable flag.

* It has no effect on columns

```cs
public class SomeForm
{
    [Updatable(false)]
    public string ReadOnlyOnUpdate { get; set; }
}
```

## DisplayName Attribute

> **namespace**: *System.ComponentModel*, **assembly**: *System*

Determines default title for grid columns or form fields.

```cs
public class SomeForm
{
    [DisplayName("Title for Some Field")]
    public string SomeField { get; set; }
}
```

* DisplayName attribute cannot be used on Enum members, so you have to use
Description attribute

* Titles set with this attribute is considered to be in *invariant* language.

> This is not a Serenity attribute, it resides in .NET System assembly.

## Description Attribute

> **namespace**: *System.ComponentModel*, **assembly**: *System*

Determines default title for enum members.

```cs
public class SomeEnum
{
    [Description("Title for Value 1")]
    Value1 = 1,
    [Description("Value 2")]
    Value2 = 2
}
```

* Titles set with this attribute is considered to be in *invariant* language.

> This is not a Serenity attribute, it resides in .NET System assembly.


## DisplayFormat Attribute

> **namespace**: *Serenity.ComponentModel*, **assembly**: *Serenity.Core*

Sets the display format for a column.

* This has no effect on editors! It is only for Display, **NOT Editing**. For editing, you have to change culture in web.config (not UI culture).

* Display format strings are specific to column data and formatter type.

* If column is a Date or DateTime column, its default formatter accepts custom DateTime format strings like *dd/MM/yyyy*.

* We don't suggest setting DisplayFormat for dates explicitly, use culture setting (not UI culture) in *web.config* unless a column has to display date/time in a different order than the default.

* You may also use following standard format strings:
    - **"d"**: `dd/MM/yyyy` where DMY order changes based on current culture.
    - **"g"**: `dd/MM/yyyy HH:mm` where DMY order changes based on current culture.
    - **"G"**: `dd/MM/yyyy HH:mm:ss` where DMY order changes based on current culture.
    - **"s"**: `yyydd-MM-ddTHH:mm:ss` ISO sortable date time format.
    - **"u"**: `yyydd-MM-ddTHH:mm:ss.fffZ` ISO 8601 UTC.

* If column is an integer, double or decimal it accepts .NET custom numeric format strings.

```cs
public class SomeColumns
{
    [DisplayFormat("d")]
    public DateTime DateWithCultureDMYOrder { get; set; }
    [DisplayFormat("dd/MM/yyyy")]
    public DateTime DateWithConstantDMYOrder { get; set; }
    [DisplayFormat("g")]
    public DateTime DateTimeToMinWithCultureDMYOrder { get; set; }
    [DisplayFormat("dd/MM/yyyy HH:mm")]
    public DateTime DateTimeToMinConstantDMYOrder { get; set; }
    [DisplayFormat("G")]
    public DateTime DateTimeToSecWithCultureDMYOrder { get; set; }
    [DisplayFormat("dd/MM/yyyy HH:mm:ss")]
    public DateTime DateTimeToSecWithConstantDMYOrder { get; set; }
    [DisplayFormat("s")]
    public DateTime SortableDateTime { get; set; }
    [DisplayFormat("u")]
    public DateTime ISO8601UTC { get; set; }
    [DisplayFormat("#,##0.00")]
    public Decimal ShowTwoZerosAfterDecimalWithGrouping { get; set; }
    [DisplayFormat("0.00")]
    public Decimal ShowTwoZerosAfterDecimalNoGrouping { get; set; }
}
```

## Placeholder Attribute

> **namespace**: *Serenity.ComponentModel*, **assembly**: *Serenity.Core*

Sets a placeholder for a form field.

* Placeholder is shown inside the editor with gray color when editor value is empty.
* Only basic input based editors and Select2 supports this. It is ignored by other editor types like Checkbox, Grid, FileUploadEditor etc.

```cs
public class SomeForm
{
    [Placeholder("Show this inside the editor when it is empty")]
    public string FieldWithPlaceHolder { get; set; }
}
```

## Hint Attribute

> **namespace**: *Serenity.ComponentModel*, **assembly**: *Serenity.Core*

Sets a hint for a form field.

* Hint is shown when field label is hovered.

* This has no effect on columns.

```cs
public class SomeForm
{
    [Hint("Show this when my caption is hovered")]
    public string FieldWithHint { get; set; }
}
```

## CssClass Attribute

> **namespace**: *Serenity.ComponentModel*, **assembly**: *Serenity.Core*

Sets CSS class for grid columns and form fields.

* In forms, class is added to container div with .field class that contains both label and editor.

* For columns, it sets cssClass property of SlickColumn, which adds this class to slick cells for all rows.

* Slick column headers are not affected by this attribute, use `[HeaderCssClass]` for that.

```cs
public class SomeForm
{
    [CssClass("extra-class")]
    public string FieldWithExtraClass { get; set; }
}

public class SomeColumn
{
    [CssClass("extra-class")]
    public string CellWithExtraClass { get; set; }
}
```

## HeaderCssClass Attribute

> **namespace**: *Serenity.ComponentModel*, **assembly**: *Serenity.Core*

Sets CSS class for grid column headers.

* This has no effect for forms.

* It sets headerCss property of SlickColumn, which adds this class to slick header for that column.

```cs
public class SomeColumn
{
    [HeaderCssClass("extra-class")]
    public string FieldWithExtraHeaderClass { get; set; }
}
```

## AlignCenter Attribute

> **namespace**: *Serenity.ComponentModel*, **assembly**: *Serenity.Core*

Centers text horizontally.

* Used to control text alignment in grids by adding `align-center` CSS class to corresponding SlickGrid column.

* Column headers are not affected by this attribute. You may use `[HeaderCssClass("align-center")]` for that.

* Note that it has no effect on editors or forms.

## AlignRight Attribute

> **namespace**: *Serenity.ComponentModel*, **assembly**: *Serenity.Core*

Right aligns text horizontally.

* Used to control text alignment in grids by adding `align-right` CSS class to corresponding SlickGrid column.

* Column headers are not affected by this attribute. You may use `[HeaderCssClass("align-right")]` for that.

* Note that it has no effect on editors or forms.

## Ignore Attribute

> **namespace**: *Serenity.ComponentModel*, **assembly**: *Serenity.Core*

Skips a property while generating grid column or form field list.

* Use this to ignore a property for UI, but still use it for other purposes like
JSON serialization.

* This might be useful when a type is used as a Service Request and Form
Declaration at the same time.

```cs
public class SomeColumns
{
    [Ignore]
    public string DontGenerateAColumnForMe { get; set; }
}
```
