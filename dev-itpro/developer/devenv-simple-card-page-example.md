---
title: Simple card page example
description: Provides AL code for a simple card type page.
author: jswymer
ms.date: 04/01/2021
ms.topic: article
ms.author: jswymer
ms.reviewer: jswymer
---

# Simple card page code example

The AL code in this article creates a simple card page that displays records from an existing table.

![Card page example.](media/sample-card-page.png "[Card page example")

For a more detailed explanation of the list page, see [Designing Card and Document Pages](devenv-designing-card-pages.md).

```AL
page 50112 SampleCustomerCard
{
    PageType = Card;
    ApplicationArea = All;
    UsageCategory = Administration;
    SourceTable = Customer;

    //Defines the names for promoted categories for actions.
    PromotedActionCategories = 'New,Process,Report,Manage,New Document,Request Approval,Customer';

    layout
    {
        area(Content)
        {
            //Defines a FastTab that has the heading 'General'.
            group(General)
            {
                field("No."; "No.")
                {
                    ApplicationArea = All;

                }
                field(Customer; Name)
                {
                    ApplicationArea = All;

                }
            }

            //Defines a FastTab that has the heading 'Contact'.
            group(Contact)
            {
                field(Name; Contact)
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

            //Defines action that display under the 'Actions' menu.
            action("New Sales Quote")
            {
                ApplicationArea = All;
                RunObject = page "Sales Quote";
                Promoted = true;
                PromotedCategory = New;
                Image = NewSalesQuote;
                trigger OnAction();
                begin

                end;
            }
            action("Banks Account")
            {
                ApplicationArea = All;
                RunObject = page "Customer Bank Account List";
                Promoted = true;

                //Promotes the action to the category named 'Customer'.
                PromotedCategory = Category7;
                Image = BankAccount;
                trigger OnAction();
                begin

                end;
            }

        }
        area(Reporting)
        {

            //Defines action that display under the 'Report' menu.
            action("Statement")
            {
                ApplicationArea = All;
                RunObject = codeunit "Customer Layout - Statement";
                trigger OnAction();
                begin

                end;
            }
        }
    }
    var
        myInt: Integer;
}
```

## Related information

[AL Development Environment](devenv-reference-overview.md)  
[Page Extension Object](devenv-page-ext-object.md)  
[Actions Overview](devenv-actions-overview.md)  
[Adding Pages and Reports to Search](devenv-al-menusuite-functionality.md)  
[Personalizing Your Workspace](/dynamics365/business-central/ui-personalization-user)  
[Use Designer](devenv-inclient-designer.md)  
