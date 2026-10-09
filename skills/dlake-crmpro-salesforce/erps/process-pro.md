---
name: dlake-crmpro-salesforce/erps/process-pro
kind: erp-summary
description: >-
  Use it when standing up or reading a Process PRO → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Process PRO: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/process-pro` (or `list_skills`) against the
Commercient admin plane. Existing customers who need access or help: contact
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
| **Location** | ERP location data becomes Commercient Location Managed Custom Object in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Location Managed Custom Object | locations |
| **GET USER** | The templates push users to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | users | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created and existing ones updated; none are deleted. | Account, Commercient Customer Master Managed Custom Object, Commercient Salesperson Managed Custom Object | customers, customer shipping addresses, salespeople |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient Customer Shipping Address Managed Custom Object | customer shipping addresses, customers |
| **Product** | ERP item location, Inventory item, location data becomes Commercient Item Location Managed Custom Object, Product in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Item Location Managed Custom Object, Product | item locations, inventory items, from, to, locations |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Invoice Header Managed Custom Object, Commercient Invoice Detail Managed Custom Object | AR invoice headers, customers, AR invoice lines |
| **Purchase Order** | The templates push Commercient Purchase Order Header Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Purchase Order Header Managed Custom Object | purchase order headers, customers |
| **Purchase Order Line** | The templates push Commercient Purchase Order Detail Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Purchase Order Detail Managed Custom Object | purchase order lines, purchase order headers |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Sales Order Header Managed Custom Object, Commercient Sales Order Detail Managed Custom Object | sales order headers, customers, sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| Process Pro Salesperson | Commercient Salesperson Managed Custom Object | Commercient external key (package 7) | 1 |
| Account | Account | Commercient AR customer code | 2 |
| Process Pro Customer Master | Commercient Customer Master Managed Custom Object | Commercient external key (package 7) | 3 |
| Customer To Account Reverse Lookup | Account | Commercient AR customer code | 4 |
| Process Pro Customer Ship To Address | Commercient Customer Shipping Address Managed Custom Object | Commercient external key (package 7) | 5 |
| Process Pro Sales Order Header | Commercient Sales Order Header Managed Custom Object | Commercient external key (package 7) | 6 |
| Process Pro Sales Order Detail | Commercient Sales Order Detail Managed Custom Object | Commercient external key (package 7) | 7 |
| Process Pro Invoice Header | Commercient Invoice Header Managed Custom Object | Commercient external key (package 7) | 8 |
| Process Pro Invoice Detail | Commercient Invoice Detail Managed Custom Object | Commercient external key (package 7) | 9 |
| Product | Product | Commercient external key (earlier package) | 10 |
| Location | Commercient Location Managed Custom Object | Commercient external key (package 7) | 11 |
| Item Location | Commercient Item Location Managed Custom Object | Commercient external key (package 7) | 12 |
| Process Pro Purchase Order Header | Commercient Purchase Order Header Managed Custom Object | External key (custom field) | 13 |
| Process Pro Purchase Order Detail | Commercient Purchase Order Detail Managed Custom Object | External key (custom field) | 14 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | salespeople |
| account feed | insert + update | customers, customer shipping addresses, salespeople |
| customer feed | insert + update | customers, salespeople |
| customer account lookup feed | insert + update | customers |
| shipping address feed | insert + update | customer shipping addresses, customers |
| sales order header feed | insert + update | sales order headers, customers |
| sales order detail feed | insert + update | sales order lines, sales order headers |
| invoice header feed | insert + update | AR invoice headers, customers |
| invoice detail feed | insert + update | AR invoice lines, AR invoice headers |
| product feed | insert + update | inventory items |
| location feed | insert + update | locations |
| item location feed | insert + update | item locations, inventory items, from, to, locations |
| purchase order feed | insert + update | purchase order headers, customers |
| purchase order line feed | insert + update | purchase order lines, purchase order headers |

## 4. Order of work

The templates set run sequence from 0 to 14. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER
- 1 — Process Pro Salesperson
- 2 — Account
- 3 — Process Pro Customer Master
- 4 — Customer To Account Reverse Lookup
- 5 — Process Pro Customer Ship To Address
- 6 — Process Pro Sales Order Header
- 7 — Process Pro Sales Order Detail
- 8 — Process Pro Invoice Header
- 9 — Process Pro Invoice Detail
- 10 — Product
- 11 — Location
- 12 — Item Location
- 13 — Process Pro Purchase Order Header
- 14 — Process Pro Purchase Order Detail

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads user sync output, salesperson sync output
- customer account lookup feed reads customer sync output
- customer feed reads salesperson sync output, account sync output
- shipping address feed reads account sync output, customer sync output
- item location feed reads product sync output, location sync output
- invoice header feed reads account sync output, customer sync output
- invoice detail feed reads invoice header sync output
- purchase order feed reads account sync output, customer sync output
- purchase order line feed reads purchase order sync output
- sales order header feed reads account sync output, customer sync output
- sales order detail feed reads sales order header sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Process Pro Salesperson | Commercient Salesperson Managed Custom Object | 33 | Row identity number → Commercient external key (package 7), Salesperson code → Name, Salesperson code → Salesperson code, Sales type → Sales type, Sales group → Sales group |
| Account | Account | 13 | Row identity number → Commercient AR customer code, company → Name, phone, Phone 2 → Phone, Address 1, address line 2 property → Billing street, city property → Billing city |
| Process Pro Customer Master | Commercient Customer Master Managed Custom Object | 97 | Row identity number → Commercient external key (package 7), company → Name, the linked Salesforce record → Account, Customer number → Customer number, company → company |
| Customer To Account Reverse Lookup | Account | 2 | Row identity number → Commercient AR customer code, the linked Salesforce record → Commercient Process Pro customer master (related record) |
| Process Pro Customer Ship To Address | Commercient Customer Shipping Address Managed Custom Object | 49 | Row identity number → Commercient external key (package 7), company → Name, Customer number → Account, Customer number → Commercient Process Pro customer master (related record), Customer number → Commercient customer number |
| Process Pro Sales Order Header | Commercient Sales Order Header Managed Custom Object | 82 | Row identity number → Commercient external key (package 7), Sales order number → Name, Customer number → Account, Customer number → Commercient Process Pro customer master (related record), Sales order number → Sales order number |
| Process Pro Sales Order Detail | Commercient Sales Order Detail Managed Custom Object | 97 | Row identity number → Commercient external key (package 7), Sales order number, transaction line number → Name, the linked Salesforce record → Commercient Process Pro sales order header (related record), Sales order number → Commercient sales order number (second field), Customer number → Commercient customer number (second field) |
| Process Pro Invoice Header | Commercient Invoice Header Managed Custom Object | 80 | Row identity number → Commercient external key (package 7), Invoice number → Name, Row identity number → Account, Row identity number → Commercient Process Pro customer master (related record), Invoice number → Commercient invoice number |
| Process Pro Invoice Detail | Commercient Invoice Detail Managed Custom Object | 72 | Row identity number → Commercient external key (package 7), Invoice number, transaction line number → Name, Row identity number → Commercient Process Pro invoice header (related record), Invoice number → Commercient invoice number (second field), Sales order number → Commercient sales order number |
| Product | Product | 5 | Row identity number → Commercient external key (earlier package), item → Name, item → Product code, Item description → Description |
| Location | Commercient Location Managed Custom Object | 10 | Row identity number → Commercient external key (package 7), Location code → Name, Location description → Description, Street address line 1 → Address 1, Street address line 2 → Address 2 |
| Item Location | Commercient Item Location Managed Custom Object | 100 | Row identity number → Commercient external key (package 7), item, Location code → Name, item → Commercient product (related record), Location code → Commercient Process Pro location (related record), Location code → Commercient location code |
| Process Pro Purchase Order Header | Commercient Purchase Order Header Managed Custom Object | 55 | Row identity number → External key (custom field), Purchase order number → Name, Purchase order number → Purchase Order Number, Vendor number → Customer and Vendor, company → Vendor Company Name |
| Process Pro Purchase Order Detail | Commercient Purchase Order Detail Managed Custom Object | 66 | Row identity number → External key (custom field), Purchase order number, transaction line number → Name, the linked Salesforce record → Commercient purchase order header (related record), Purchase order number → Purchase Order Number, Unit of measure → Unit of Measure short description |

## 6. Community templates

The catalogue carries 15 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 15
- Default operations: insert on 15, update on 15, delete on 15
- Marked as circular sync: 0
- Licence groups they span: 10
- Destination objects: Account, Commercient Customer Shipping Address Managed Custom Object,
  Commercient Customer Master Managed Custom Object, Commercient Invoice Header Managed Custom
  Object, Commercient Invoice Detail Managed Custom Object, Commercient Item Location Managed Custom
  Object, Commercient Location Managed Custom Object, Commercient Sales Order Header Managed Custom
  Object, Commercient Salesperson Managed Custom Object, Commercient Sales Order Detail Managed
  Custom Object, Commercient Purchase Order Header Managed Custom Object, Commercient Purchase Order
  Detail Managed Custom Object, Product and a custom object
- Object display names: Account, Customer To Account Reverse Lookup, Process Pro Customer Master,
  Process Pro Customer Ship To Address, Process Pro Invoice Detail, Process Pro Invoice Header,
  Process Pro Item Location, Process Pro Item Lot, Process Pro Location, Process Pro Purchase Order
  Detail, Process Pro Purchase Order Header, Process Pro Sales Order Detail and 3 more
- Template groups: Account, Product, Invoice, Purchase Order, Sales order, Customer Multi Ship
  Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/process-pro`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Process PRO → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/process-pro`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
