---
title: Work with repeater controls
description: A repeater is a control used to define a list of records from the source table of a page.
author: jswymer
ms.author: jswymer
ms.custom: bap-template
ms.date: 11/24/2022
ms.reviewer: jswymer

ms.topic: how-to
---

# Work with repeater controls

A repeater control is used to define a list of records from the source table of a page. Records are displayed as rows and columns, where each row is a record and each column is a field.

You add a `repeater()` control within the `area(Content)` control of a page, and then you nest a `field()` control for each of the fields from the table specified in the [SourceTable Property](properties/devenv-sourcetable-property.md) that you want to include. The order of the field controls determines the order in which they appear on the page.

A list is a characteristic element of the layout of pages of the type **[List](devenv-simple-list-page-example.md)** and **[ListPart](devenv-designing-listparts.md)**. You define the type of a page using the [PageType Property](properties/devenv-pagetype-property.md). List pages are designed for using a single `repeater()` control, which must be defined at the beginning of the content area. If you include more than one repeater or another control like a group or grid, the page might not behave as expected. If you want to design a page that includes controls in the content area other than a repeater, then try using a **Worksheet** page type instead. For more information, see [Pages Types and Layout](devenv-page-types-and-layouts.md).

Pages of the type **[API](devenv-api-pagetype.md)** are also designed to integrate a repeater control, but they can't be displayed in the user interface.

The following figure illustrates how a list created by a repeater is displayed in the Web Client.

![List part overview.](media/sample-list-part.png "List part overview")

## Limitations

- The Web client doesn't support displaying `repeater` controls that contain other Parts or FlowFilter fields.
- The repeater control is only supported by the Web client in [collection-oriented pages](devenv-page-types-and-layouts.md#collection-oriented-pages) List, Worksheet, and ListPart. The `repeater` control isn't supported on [entity-oriented page types](devenv-page-types-and-layouts.md#entity-oriented-pages), like `Card`, `CardPart`, `Document`, or `Navigation`. 
  In fact, if you use a repeater on an entity-oriented page, you'll get [UICop Warning AW0008](analyzers/uicop-aw0008.MD). If you want to include data as a list on one of these page type, create a `ListPart` page that contains `repeater` control, and then use the `ListPart` in the entity-oriented page.

## How to customize a list

You can make various changes to customize the appearance of the list in a page and determine which data is displayed. The changes can be done using AL code or directly from the Web Client.

To improve the visibility and readability of the list, you can set some properties at control and field level. For example, you can specify the width of a column using the [Width Property](properties/devenv-width-property.md). Or, you could set row indentation using the [IndentationColumn Property](properties/devenv-IndentationColumn-property.md) and [IndentationControls Property](properties/devenv-IndentationControls-property.md).

You should also consider limiting the number of columns and enabling a freeze column by setting the [FreezeColumn Property](properties/devenv-freezecolumn-property.md). Apply filters to display only the most relevant data and minimize scrolling. This practice is important when it comes to mobile devices, where there are restrictions as to how much content can be shown. You can also limit data and scrolling by defining a new view on a page. For more information, go to [Views](devenv-views.md).

Some other alternative things you can do include:

- Add, move, or hide fields and make adjustments like modifying the column width, directly from the user interface.

  To apply these changes, use the Designer tool. Go to [Use Designer](devenv-inclient-designer.md). If these changes are meant for your own workspace, then you use the Personalize tool. For more information, see [Personalizing Your Workspace](/dynamics365/business-central/ui-personalization-user).
- Use the filter pane from the page to sort the data and apply filters. Go to [Sorting, Searching, and Filtering](/dynamics365/business-central/ui-enter-criteria-filters).
- Use the Tile View to display records as tiles (or bricks) instead of as rows and optimize the space and readability of data. For more information on how to customize the tile view, see [Displaying Data as Tiles](devenv-lists-as-tiles.md).

## Example

The following example shows how the repeater control is used to define a list page for the Customer table.

```AL
page 50111 SampleCustomerList
{
    PageType = List;
    ApplicationArea = All;
    SourceTable = Customer;
    UsageCategory = Lists;
    CardPageId = 50112;
    Caption = 'Sample Customers';

    layout
    {
        area(Content)
        {
            // Sets the No., Name, Contact, and Phone No. fields in the Customer table to be displayed as columns in the list. 
            repeater(Group)
            {
                field("No."; "No.")
                {
                    ApplicationArea = All;

                }
                field(Name; Name)
                {
                    ApplicationArea = All;

                }
                field(Contact; Contact)
                {
                    ApplicationArea = All;
                }

                field(Phone; "Phone No.")
                {
                    ApplicationArea = All;

                }
            }
        }
    }

    actions
    {
        area(Processing)
        {
            action("Ledger Entries")
            {
                ApplicationArea = All;
                RunObject = page "Customer Ledger Entries";
                trigger OnAction();
                begin

                end;
            }
        }
    }

}
```

You can use page customizations to modify the layout of a page for concrete users. The following code hides the `"Phone No."` column from the users with `Accountant` profile.

```AL
profile Accountant
{
    Description = 'Functionality for finance staff performing any AR or AP work and managerial reporting.';
    RoleCenter = "Accountant Role Center";
    Customizations = MyCustomization;
}

pagecustomization MyCustomization customizes "Customer List"
{
    layout
    {
        modify("Phone No.")
        {
            Visible = false;
        }
    }
}
```

## Related information

[Pages Overview](devenv-pages-overview.md)  
[Designing List Pages](devenv-designing-list-pages.md)  
[Page Customization Object](devenv-page-customization-object.md)
