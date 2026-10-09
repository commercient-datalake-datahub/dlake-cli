---
name: dlake-crmpro-salesforce/erps/epicor-10
kind: erp-summary
description: >-
  Use it when standing up or reading an Epicor 10 → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Epicor 10: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/epicor-10` (or `list_skills`) against the
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
| **Get Users** | The templates push user to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | user | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created and existing ones updated; none are deleted. | Account, Contact, Commercient Epicor 10 Customer Managed Custom Object, Commercient Epicor 10 Sales Rep Managed Custom Object | customers, customer contacts, sales reps |
| **CRM Opportunity and Line** | The templates push Opportunity, Opportunity line item to Salesforce. New records are created and existing ones updated; none are deleted. | Opportunity, Opportunity line item | quote headers, quote lines |
| **CRM Order and Line** | ERP order header custom fields, order headers, quote header records data becomes Order, Order product in Salesforce. New records are created and existing ones updated; none are deleted. | Order, Order product | sales order headers, quote headers, sales order lines |
| **CRM Quote and Line** | The templates push Quote, Quote line item to Salesforce. New records are created and existing ones updated; none are deleted. | Quote, Quote line item | quote headers, order entry headers, customers, quote lines |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient Epicor 10 Ship To Managed Custom Object | shipping addresses, customers |
| **Product** | ERP Part, part warehouses data becomes Commercient Epicor 10 Item Master Managed Custom Object, Commercient Epicor 10 Item Warehouse Managed Custom Object, Product in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Epicor 10 Item Master Managed Custom Object, Commercient Epicor 10 Item Warehouse Managed Custom Object, Product | parts, part warehouse quantities |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Epicor 10 Invoice Header Managed Custom Object, Commercient Epicor 10 Invoice Detail Managed Custom Object | invoice headers, invoice lines |
| **Pricebook** | ERP Part data becomes Price book entry, Price book entry in Salesforce. New records are created and existing ones updated; none are deleted. | Price book entry | parts |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Epicor 10 Sales Order Header Managed Custom Object, Commercient Epicor 10 Sales Order Detail Managed Custom Object | sales order headers, sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Get Users | user | — | 1 |
| Epicor 10 Sales Person | Commercient Epicor 10 Sales Rep Managed Custom Object | Commercient external key (Epicor package) | 1 |
| Account | Account | Commercient AR customer code | 2 |
| Epicor 10 Customer | Commercient Epicor 10 Customer Managed Custom Object | Commercient external key (Epicor package) | 3 |
| Account Reverse Lookup | Account | Commercient AR customer code | 4 |
| Epicor 10 Shipping address | Commercient Epicor 10 Ship To Managed Custom Object | Commercient external key (Epicor package) | 5 |
| Product | Product | Commercient external key (earlier package) | 6 |
| Create Standard Price Book | Price book entry | External key (custom field) | 7 |
| Update Standard Pricebook | Price book entry | External key (custom field) | 8 |
| Epicor 10 Item Master | Commercient Epicor 10 Item Master Managed Custom Object | Commercient external key (Epicor package) | 9 |
| Epicor 10 Item Warehouse | Commercient Epicor 10 Item Warehouse Managed Custom Object | Commercient external key (Epicor package) | 10 |
| Product Reverse Lookup | Product | Commercient external key (earlier package) | 11 |
| Epicor 10 Sales Order Header | Commercient Epicor 10 Sales Order Header Managed Custom Object | Commercient external key (Epicor package) | 12 |
| Epicor 10 Sales Order Detail | Commercient Epicor 10 Sales Order Detail Managed Custom Object | Commercient external key (Epicor package) | 13 |
| Epicor 10 Invoice Header | Commercient Epicor 10 Invoice Header Managed Custom Object | Commercient external key (Epicor package) | 14 |
| Epicor 10 Invoice Detail | Commercient Epicor 10 Invoice Detail Managed Custom Object | Commercient external key (Epicor package) | 15 |
| Contacts | Contact | External key (custom field) | 16 |
| Opportunity | Opportunity | External key (custom field) | 19 |
| Opportunity line item | Opportunity line item | External key (custom field) | 20 |
| Quote | Quote | External key (custom field) | 22 |
| Quote Line | Quote line item | External key (custom field) | 23 |
| Standard Order | Order | External key (custom field) | 45 |
| Standard order line | Order product | External key (custom field) | 46 |
| Standard Order Update | Order | External key (custom field) | 47 |
| Standard order line update | Order product | External key (custom field) | 48 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | sales reps |
| account feed | insert + update | customers |
| customer sync output | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| shipping address feed | insert + update | shipping addresses, customers |
| product feed | insert + update | parts, part warehouse quantities |
| standard price book feed | insert + update | parts |
| standard price book update feed | insert + update | parts |
| item master feed | insert + update | parts |
| item warehouse feed | insert + update | part warehouse quantities |
| item master product lookup feed | insert only | parts |
| sales order feed | insert + update | sales order headers |
| sales order line feed | insert + update | sales order lines |
| invoice feed (Commercient objects) | insert + update | invoice headers |
| invoice line feed (Commercient objects) | insert + update | invoice lines |
| contact feed | insert + update | customer contacts |
| opportunity feed | insert + update | quote headers |
| opportunity line item feed | insert + update | quote lines |
| quote feed | insert + update | quote headers, order entry headers, customers |
| quote line item feed | insert + update | quote lines |
| standard order feed | insert + update | sales order headers, quote headers |
| standard order line feed | insert + update | sales order lines |
| standard order update feed | insert + update | sales order headers, quote headers |
| standard order line update feed | insert + update | sales order lines |

## 4. Order of work

The templates set run sequence from 1 to 48. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Get Users, Epicor 10 Sales Person
- 2 — Account
- 3 — Epicor 10 Customer
- 4 — Account Reverse Lookup
- 5 — Epicor 10 Shipping address
- 6 — Product
- 7 — Create Standard Price Book
- 8 — Update Standard Pricebook
- 9 — Epicor 10 Item Master
- 10 — Epicor 10 Item Warehouse
- 11 — Product Reverse Lookup
- 12 — Epicor 10 Sales Order Header
- 13 — Epicor 10 Sales Order Detail
- 14 — Epicor 10 Invoice Header
- 15 — Epicor 10 Invoice Detail
- 16 — Contacts
- 19 — Opportunity
- 20 — Opportunity line item
- 22 — Quote
- 23 — Quote Line
- 45 — Standard Order
- 46 — Standard order line
- 47 — Standard Order Update
- 48 — Standard order line update

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads salesperson sync output, user sync output
- customer account lookup feed reads customer sync output
- contact feed reads account sync output
- opportunity feed reads account sync output
- opportunity line item feed reads product sync output, opportunity sync output (generic name)
- standard order feed reads account sync output, contact sync output, opportunity sync output, quote
  sync output; no template in this set writes opportunity sync output, quote sync output
- standard order line feed reads standard order sync output, product sync output, standard price
  book sync output, quote line sync output; no template in this set writes quote line sync output
- standard order update feed reads account sync output, contact sync output, opportunity sync
  output, quote sync output; no template in this set writes opportunity sync output, quote sync
  output
- standard order line update feed reads standard order sync output, product sync output, standard
  price book sync output, quote line sync output; no template in this set writes quote line sync
  output
- quote feed reads account sync output, opportunity sync output (generic name)
- quote line item feed reads Salesforce quote sync output (generic name), product sync output
- customer sync output reads account sync output, salesperson sync output
- shipping address feed reads account sync output, customer sync output
- item master feed reads product sync output
- item warehouse feed reads product sync output, item master sync output
- invoice feed (Commercient objects) reads account sync output, customer sync output, salesperson
  sync output
- invoice line feed (Commercient objects) reads invoice sync output (Commercient objects)
- standard price book feed reads product sync output
- standard price book update feed reads product sync output
- item master product lookup feed reads item master sync output
- sales order feed reads account sync output, customer sync output, salesperson sync output
- sales order line feed reads sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Epicor 10 Sales Person | Commercient Epicor 10 Sales Rep Managed Custom Object | 50 | Company, Sales rep code → Commercient external key (Epicor package), Name → Name, Address 1 → Address 1, Address 2 → Address 2, Address 3 → Address 3 |
| Account | Account | 15 | Company, Customer number → Commercient AR customer code, Name → name, Sales rep code → Commercient Epicor 10 sales rep (related record), Sales rep code → owner lookup, Phone number → Phone |
| Epicor 10 Customer | Commercient Epicor 10 Customer Managed Custom Object | 111 | Company, Customer number → Commercient external key (Epicor package), Name → Name, Branch identifier → Branch identifier, Customer pricing schema → Customer pricing schema, → |
| Account Reverse Lookup | Account | 2 | Company, Customer number → Commercient AR customer code, the linked Salesforce record → Commercient Epicor 10 customer master (related record) |
| Epicor 10 Shipping address | Commercient Epicor 10 Ship To Managed Custom Object | 92 | Company, Customer number, Shipping address number → Commercient external key (Epicor package), Name → Name, Additional handling flag → Additional handling flag, Address 1 → Address 1, Address 2 → Address 2 |
| Product | Product | 7 | Company,Part number → Commercient external key (earlier package), Part description → Name, Part number → Product code, Inactive → Active, Part description → Description |
| Create Standard Price Book | Price book entry | 5 | Company, Part number → External key (custom field), the linked Salesforce record → price book lookup, the linked Salesforce record → product lookup, Inactive → Active, Unit price → Unit price |
| Update Standard Pricebook | Price book entry | 4 | Company, Part number → External key (custom field), the linked Salesforce record → product lookup, Unit price → Unit price |
| Epicor 10 Item Master | Commercient Epicor 10 Item Master Managed Custom Object | 158 | Company, Part number → Commercient external key (Epicor package), the linked Salesforce record → Commercient product (related record), Company, Part number → Name, Company → Company, Part number → Part number |
| Epicor 10 Item Warehouse | Commercient Epicor 10 Item Warehouse Managed Custom Object | 52 | Company, Part number, Warehouse code → Commercient external key (Epicor package), Company, Part number → Commercient product (related record), Company, Part number → Commercient Epicor 10 item master (related record), Demand quantity → Commercient demand quantity, Reserved quantity → Commercient reserved quantity |
| Product Reverse Lookup | Product | 2 | Company,Part number → Commercient external key (earlier package), the linked Salesforce record → Commercient Epicor 10 item master (related record) |
| Epicor 10 Sales Order Header | Commercient Epicor 10 Sales Order Header Managed Custom Object | 97 | Company, Order number → Commercient external key (Epicor package), Company, Order number → Name, Apply charges → Apply charges, AR letter of credit identifier → AR letter of credit identifier, Bill to contact number → Bill to contact number |
| Epicor 10 Sales Order Detail | Commercient Epicor 10 Sales Order Detail Managed Custom Object | 94 | Company, Order line number, Order number → Commercient external key (Epicor package), Company, Order line number, Order number → Name, Advance billing balance → Advance billing balance, Base part number → Base part number, Base revision number → Base revision number |
| Epicor 10 Invoice Header | Commercient Epicor 10 Invoice Header Managed Custom Object | 90 | Company,Invoice number → Commercient external key (Epicor package), Company,Invoice number → Name, Legal number → Legal number, Apply date → Apply date, Billing contact number → Billing contact number |
| Epicor 10 Invoice Detail | Commercient Epicor 10 Invoice Detail Managed Custom Object | 87 | Company,invoice line,Invoice number → Commercient external key (Epicor package), Company,invoice line,Invoice number → Name, →, Advance billing credit → Advance billing credit, Advance billing gain or loss → Advance billing gain or loss |
| Contacts | Contact | 8 | Company, Customer number, Shipping address number, Contact number → External key (custom field), the linked Salesforce record → account lookup, Family name, Name → Family name, Given name, Name → Given name, Suffix → Suffix |
| Opportunity | Opportunity | 6 | Company, Quote number → External key (custom field), the linked Salesforce record → account lookup, Quote number → Name, Expiry date → close date property |
| Opportunity line item | Opportunity line item | 7 | Part number → product lookup, Quote number → Opportunity, Price Book → price book entry lookup, Company, Quote number, Quote line number → external key column, Pricing value → Unit price |
| Quote | Quote | 17 | the linked opportunity → Opportunity, the linked account → account lookup, the linked price book → price book lookup, Status → Status, phone number of the linked record → Phone |
| Quote Line | Quote line item | 6 | Company, Quote number, Quote line number → External key (custom field), Part number → product lookup, Price Book → price book entry lookup, Quote number → Quote, Order quantity → Quantity |
| Standard Order | Order | 28 | Company, Order number → External key (custom field), Customer number → account lookup, Bill to contact number → bill to contact lookup, Ship to contact number → ship to contact lookup, Quote number → Opportunity |
| Standard order line | Order product | 12 | Company, Order line number, Order number → External key (custom field), the linked Salesforce record → Order identifier, the linked Salesforce record → product lookup, the linked Salesforce record → price book entry lookup, the linked Salesforce record → quote line item lookup |
| Standard Order Update | Order | 27 | Company, Order number → External key (custom field), Customer number → account lookup, Bill to contact number → bill to contact lookup, Ship to contact number → ship to contact lookup, Quote number → Opportunity |
| Standard order line update | Order product | 10 | Company, Order line number, Order number → External key (custom field), the linked Salesforce record → Order identifier, the linked Salesforce record → quote line item lookup, Epicor line description → Description, Request date → End date |

## 6. Community templates

The catalogue carries 430 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 430
- Default operations: insert on 430, update on 430, delete on 430
- Marked as circular sync: 6
- Licence groups they span: 17
- Destination objects: Account, Price book entry, Contact, Product, Commercient Epicor 10 Customer
  Managed Custom Object, Commercient Epicor 10 Invoice Detail Managed Custom Object, Commercient
  Epicor 10 Invoice Header Managed Custom Object, Commercient Epicor 10 Sales Order Detail Managed
  Custom Object, Commercient Epicor 10 Sales Order Header Managed Custom Object, Commercient Epicor
  10 Sales Rep Managed Custom Object, Commercient Epicor 10 Ship To Managed Custom Object,
  Commercient Epicor 10 Item Master Managed Custom Object, Commercient Epicor 10 Item Warehouse
  Managed Custom Object, Opportunity, Order product, Order, Quote, Commercient Epicor 10 Quote
  Detail Managed Custom Object, Commercient Epicor 10 Quote Header Managed Custom Object, 42 more
  and 16 custom objects
- Object display names: Account, Account Reverse Lookup, Epicor 10 Customer, Epicor 10 Invoice
  Header, Epicor 10 Sales Order Detail, Epicor 10 Sales Order Header, Epicor 10 Shipping address,
  Contacts, Create Standard Price Book, Epicor 10 Invoice Detail, Epicor 10 Sales Person, Product,
  85 more and 23 further templates
- Template groups: Account, Product, Sales order, Invoice, Customer Multi Ship Addresses, CRM Order
  and Line, CRM Opportunity and Line, CRM Quote and Line, Opportunity, File or Document Sync,
  Pricebook

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/epicor-10`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Epicor 10 → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/epicor-10`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
