---
name: dlake-crmpro-salesforce/erps/microsoft-dynamics-ax
kind: erp-summary
description: >-
  Use it when standing up or reading a Microsoft Dynamics AX → Salesforce template set, when
  deciding which templates to import and activate, or when a run completes without pushing records
  and the answer is in the view or the configuration row. It extends dlake-crmpro, which covers
  operating CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a
  child of, which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Microsoft Dynamics AX: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/microsoft-dynamics-ax` (or `list_skills`)
against the Commercient admin plane. Existing customers who need access or help: contact
support@commercient.com. New customers: contact sales@commercient.com to become a customer and be
whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-salesforce is the
destination skill this page is a child of, and the authority for the Salesforce conventions that
hold across every ERP: read it first, then come back here for what this source's own templates set.
This page grows as the catalogue does.

## 1. What the templates deliver

| Group | Business outcome | Objects | Source tables and views |
|---|---|---|---|
| **Sync Sales quotation** | The templates push Commercient Sales Quotation Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Sales Quotation Managed Custom Object | sales quotations |
| **Get Salesforce User** | The templates push User to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | User | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Commercient Dynamics AX Payment Terms Managed Custom Object, Contact, Commercient Dynamics AX Customer Managed Custom Object, Commercient Dynamics AX Salesperson Managed Custom Object | customers, company information records, payment terms, contact persons |
| **CRM Opportunity and Line** | The templates push opportunity sync output (generic name) to Salesforce. New records are created and existing ones updated; none are deleted. | opportunity sync output (generic name) | opportunities, customers |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient Dynamics AX Address Managed Custom Object | addresses |
| **Product** | The templates push Commercient Dynamics AX Item Warehouse Managed Custom Object, Commercient Dynamics AX Item Managed Custom Object, Product to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Dynamics AX Item Warehouse Managed Custom Object, Commercient Dynamics AX Item Managed Custom Object, Product | warehouses, items |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Dynamics AX Invoice Header Managed Custom Object, Commercient Dynamics AX Invoice Detail Managed Custom Object | sales order lines, customer invoice lines, customer invoice headers |
| **Pricebook** | The templates push Price book entry to Salesforce. New records are created and existing ones updated; none are deleted. | Price book entry | items |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Dynamics AX Sales Order Header Managed Custom Object, Commercient Dynamics AX Sales Order Detail Managed Custom Object | sales orders, S, sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Get Salesforce User | User | Commercient salesperson code | 0 |
| Sync Payment term | Commercient Dynamics AX Payment Terms Managed Custom Object | Commercient external key (Dynamics NAV package) | 1 |
| Sync Address | Commercient Dynamics AX Address Managed Custom Object | Commercient external key (Dynamics NAV package) | 2 |
| Microsoft Dynamics AX Salesperson | Commercient Dynamics AX Salesperson Managed Custom Object | Commercient external key (Dynamics NAV package) | 3 |
| Account | Account | Commercient AR customer code | 4 |
| Sync Customer | Commercient Dynamics AX Customer Managed Custom Object | Commercient external key (Dynamics NAV package) | 5 |
| Sync Customer to account lookup | Account | Commercient AR customer code | 6 |
| Sync Item warehouse | Commercient Dynamics AX Item Warehouse Managed Custom Object | Commercient external key (Dynamics NAV package) | 7 |
| Sync Product object | Product | Commercient external key (earlier package) | 8 |
| Sync Item | Commercient Dynamics AX Item Managed Custom Object | Commercient external key (Dynamics NAV package) | 9 |
| Sync Item to product lookup | Product | Commercient external key (earlier package) | 10 |
| Sync standard price book sync output (generic name) | Price book entry | External key (custom field) | 11 |
| Sync Standard price book update | Price book entry | External key (custom field) | 12 |
| Sync opportunity sync output (generic name) | opportunity sync output (generic name) | External key (custom field) | 13 |
| Sync Sales order header | Commercient Dynamics AX Sales Order Header Managed Custom Object | Commercient external key (Dynamics NAV package) | 15 |
| Sync sales order detail | Commercient Dynamics AX Sales Order Detail Managed Custom Object | Commercient external key (Dynamics NAV package) | 16 |
| Sync invoice header | Commercient Dynamics AX Invoice Header Managed Custom Object | Commercient external key (Dynamics NAV package) | 17 |
| Sync invoice detail | Commercient Dynamics AX Invoice Detail Managed Custom Object | Commercient external key (Dynamics NAV package) | 18 |
| Sync Sales quotation | Commercient Sales Quotation Managed Custom Object | Commercient external key (Dynamics NAV package) | 19 |
| Contact | Contact | External key (custom field) | 19 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| payment terms feed | insert + update | payment terms |
| address feed | insert + update | addresses |
| salesperson feed | insert + update | contact persons |
| account feed | insert + update | customers, company information records |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| item warehouse feed | insert + update | warehouses |
| product feed | insert + update | items |
| item feed | insert + update | items |
| item product lookup feed | insert + update | items |
| standard price book feed | insert + update | items |
| standard price book feed (changes) | insert + update | items |
| opportunity feed | insert + update | opportunities, customers |
| sales order feed | insert + update | sales orders, S |
| sales order line feed | insert + update | sales order lines |
| sales order line feed | insert + update | sales order lines |
| invoice line feed | insert + update | customer invoice lines, customer invoice headers |
| contact feed | insert + update | contact persons |

## 4. Order of work

The templates set run sequence from 0 to 19. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — Get Salesforce User
- 1 — Sync Payment term
- 2 — Sync Address
- 3 — Microsoft Dynamics AX Salesperson
- 4 — Account
- 5 — Sync Customer
- 6 — Sync Customer to account lookup
- 7 — Sync Item warehouse
- 8 — Sync Product object
- 9 — Sync Item
- 10 — Sync Item to product lookup
- 11 — Sync standard price book sync output (generic name)
- 12 — Sync Standard price book update
- 13 — Sync opportunity sync output (generic name)
- 15 — Sync Sales order header
- 16 — Sync sales order detail
- 17 — Sync invoice header
- 18 — Sync invoice detail
- 19 — Sync Sales quotation, Contact

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- customer account lookup feed reads customer sync output
- opportunity feed reads account sync output
- customer feed reads account sync output, payment terms sync output
- item feed reads product sync output
- sales order line feed reads sales order sync output, item sync output, sales order line sync
  output
- invoice line feed reads invoice sync output, item sync output
- standard price book feed reads product sync output
- standard price book feed (changes) reads product sync output, standard price book sync output
- item product lookup feed reads item sync output
- sales order feed reads account sync output, customer sync output, salesperson sync output
- sales order line feed reads sales order sync output, item sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Sync Payment term | Commercient Dynamics AX Payment Terms Managed Custom Object | 17 | Company data area, Payment terms code → Commercient external key (Dynamics NAV package), Company data area, Payment terms code → Name, Payment terms code → Commercient payment terms code, Payment method → Commercient payment method, Number of days → Commercient number of days |
| Sync Address | Commercient Dynamics AX Address Managed Custom Object | 15 | Company data area,Record identifier → Commercient external key (Dynamics NAV package), Company data area,Record identifier → Commercient name, Address → Commercient address, Country or region → Commercient country or region, Zip code → Commercient postal code |
| Microsoft Dynamics AX Salesperson | Commercient Dynamics AX Salesperson Managed Custom Object | 8 | Record identifier → Commercient external key (Dynamics NAV package), Name → Name, Created by → Created by, First name → First name, Last name → Last name |
| Account | Account | 14 | Company data area, Account number → Commercient AR customer code, Company data area, Name → Company account (custom field) (an installation specific custom field; the shipped template names one customer org), Name → Name, Phone → Phone, Address → Billing street |
| Sync Customer | Commercient Dynamics AX Customer Managed Custom Object | 56 | Company data area,Account number → Commercient external key (Dynamics NAV package), Account number → Account, Name → Name, Invoice account → Invoice account, Customer group → Customer group |
| Sync Customer to account lookup | Account | 2 | Company data area, Account number → Commercient AR customer code, the linked Salesforce record → Commercient Dynamics AX Customer Managed Custom Object |
| Sync Item warehouse | Commercient Dynamics AX Item Warehouse Managed Custom Object | 32 | Company data area,Warehouse code → Commercient external key (Dynamics NAV package), Name → Name, Warehouse code → Warehouse code, Manual → Manual, Empty pallet location → Empty pallet location |
| Sync Product object | Product | 4 | Company data area, Item identifier → Commercient external key (earlier package), Item identifier → Product code, Item name → Name, Item name → Description |
| Sync Item | Commercient Dynamics AX Item Managed Custom Object | 64 | Company data area, Item identifier → Commercient external key (Dynamics NAV package), Company data area, Item identifier, Item name → Name, Item identifier → Commercient item number, Item type → Commercient item type, Purchase model → Commercient purchase model |
| Sync Item to product lookup | Product | 2 | Company data area, Item identifier → Commercient external key (earlier package), the linked Salesforce record → Commercient Dynamics AX Item Managed Custom Object |
| Sync standard price book sync output (generic name) | Price book entry | 6 | Company data area, Item identifier → External key (custom field), the linked Salesforce record → price book lookup, the linked Salesforce record → product lookup, Active → Active, Unit price → Unit price |
| Sync Standard price book update | Price book entry | 4 | Company data area, Item identifier → External key (custom field), Active → Active, Unit price → Unit price, Row timestamp → Row timestamp |
| Sync opportunity sync output (generic name) | opportunity sync output (generic name) | 8 | Company data area, Opportunity identifier → External key (custom field), Company data area, Opportunity identifier → Name, Subject → Description, Status → Stage, Estimated revenue → Budget (custom field) |
| Sync Sales order header | Commercient Dynamics AX Sales Order Header Managed Custom Object | 73 | Company data area,Sales order number → Commercient external key (Dynamics NAV package), Sales order number → Commercient sales order number, Sales name → Commercient sales name, Reservation → Commercient reservation, Customer account → Commercient customer account |
| Sync sales order detail | Commercient Dynamics AX Sales Order Detail Managed Custom Object | 77 | Company data area,Inventory transaction identifier → Commercient external key (Dynamics NAV package), Company data area,Inventory transaction identifier → Commercient name, Sales order number → Commercient sales order number, ERP line number → Commercient line number, Item identifier → Commercient item number |
| Sync invoice header | Commercient Dynamics AX Invoice Header Managed Custom Object | 74 | Company data area,Inventory transaction identifier → Commercient external key (Dynamics NAV package), Sales order number → Commercient sales order header, Item identifier → Commercient item, ERP line number → Commercient line number, Sales status → Commercient sales status |
| Sync invoice detail | Commercient Dynamics AX Invoice Detail Managed Custom Object | 86 | Company data area, Record identifier → Commercient external key (Dynamics NAV package), Invoice identifier → Commercient invoice number, Invoice date → Commercient invoice date, ERP line number → Commercient invoice line number, Inventory transaction identifier → Commercient inventory transaction identifier |
| Contact | Contact | 12 | Given name → Given name, Family name → Family name, Title → Title, Email → Email, Phone → Phone |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/microsoft-dynamics-ax`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Microsoft Dynamics AX → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/microsoft-dynamics-ax`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
