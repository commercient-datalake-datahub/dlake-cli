---
name: dlake-crmpro-salesforce/erps/epicor-prophet-21-p21
kind: erp-summary
description: >-
  Use it when standing up or reading an Epicor Prophet 21 (Prophet 21) → Salesforce template set,
  when deciding which templates to import and activate, or when a run completes without pushing
  records and the answer is in the view or the configuration row. It extends dlake-crmpro, which
  covers operating CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is
  a child of, which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Epicor Prophet 21 (Prophet 21): what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/epicor-prophet-21-p21` (or `list_skills`)
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
| **Ship to** | ERP Customer, shipping address data becomes Commercient Prophet 21 Ship To Address Managed Custom Object in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Prophet 21 Ship To Address Managed Custom Object | shipping addresses, customers |
| **Inv Master** | The templates push Commercient Prophet 21 Inventory Master Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Prophet 21 Inventory Master Managed Custom Object | inventory items |
| **Product** | ERP inventory master data becomes Product in Salesforce. New records are created and existing ones updated; none are deleted. | Product | inventory items |
| **Warehouse** | ERP inventory location, inventory master, location data becomes Commercient Prophet 21 Warehouse Managed Custom Object in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Prophet 21 Warehouse Managed Custom Object | inventory locations, inventory items, locations |
| **Product item Reverse lookup** | ERP inventory master data becomes Commercient Prophet 21 Inventory Master Managed Custom Object in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Prophet 21 Inventory Master Managed Custom Object | inventory items |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created and existing ones updated; none are deleted. | Account, Commercient Prophet 21 Terms Managed Custom Object, Contact, Commercient Prophet 21 Customer Managed Custom Object, Commercient Prophet 21 Salesperson Managed Custom Object | customers, addresses, shipping addresses, terms, contacts |
| **CRM Opportunity and Line** | ERP order entry header, quote header, inventory master data becomes Opportunity, Opportunity line item in Salesforce. New records are created and existing ones updated; none are deleted. | Opportunity, Opportunity line item | quote headers, order entry headers, quote lines, inventory items |
| **CRM Order and Line** | ERP Address, order entry header, quote header data becomes Order, Order product in Salesforce. New records are created and existing ones updated; none are deleted. | Order, Order product | order entry headers, addresses, quote headers, order entry lines, inventory items |
| **CRM Quote and Line** | ERP Address, order entry header, quote header data becomes Quote, Quote line item in Salesforce. New records are created and existing ones updated; none are deleted. | Quote, Quote line item | quote headers, order entry headers, addresses, quote lines, order entry lines, inventory items |
| **Invoice History Headers** | The Invoices from the ERP invoice module are synchronized to the Commercient Invoice Header (MCO) object in CRM. Customer service and sales people can visualize the status of the Invoice such as open, closed, as well as the balance remaining and the due date. New records are created and existing ones updated; none are deleted. | Commercient Prophet 21 Invoice Header Managed Custom Object | invoice headers |
| **Open AR Invoice Header** | The detail lines on open invoices are visible too so that you are aware of what items you are awaiting payment on from your customer. New records are created and existing ones updated; none are deleted. | Commercient Prophet 21 Invoice Line Managed Custom Object | invoice lines |
| **Pricebook** | ERP inventory master data becomes Price book object, Price book entry, Price book entry in Salesforce. New records are created and existing ones updated; none are deleted. | Price book object, Price book entry | inventory items |
| **Sales order** | The templates push Commercient Prophet 21 Sales Order Detail Managed Custom Object, Commercient Prophet 21 Sales Order Header Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Prophet 21 Sales Order Detail Managed Custom Object, Commercient Prophet 21 Sales Order Header Managed Custom Object | order entry lines, order entry headers |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Terms code | Commercient Prophet 21 Terms Managed Custom Object | Commercient external key (Epicor package) | 1 |
| Salesperson | Commercient Prophet 21 Salesperson Managed Custom Object | Commercient external key (Epicor package) | 2 |
| Account | Account | Commercient AR customer code | 3 |
| Customer | Commercient Prophet 21 Customer Managed Custom Object | Commercient external key (Epicor package) | 4 |
| Ship to | Commercient Prophet 21 Ship To Address Managed Custom Object | Commercient external key (Epicor package) | 5 |
| Order header | Commercient Prophet 21 Sales Order Header Managed Custom Object | Commercient external key (Epicor package) | 6 |
| Order line number | Commercient Prophet 21 Sales Order Detail Managed Custom Object | Commercient external key (Epicor package) | 7 |
| Invoice header | Commercient Prophet 21 Invoice Header Managed Custom Object | Commercient external key (Epicor package) | 8 |
| invoice line | Commercient Prophet 21 Invoice Line Managed Custom Object | Commercient external key (Epicor package) | 9 |
| Customer Reverse Lookup Account | Account | Commercient AR customer code | 10 |
| Inv Master | Commercient Prophet 21 Inventory Master Managed Custom Object | Commercient external key (Epicor package) | 11 |
| Product | Product | Commercient external key (earlier package) | 12 |
| Warehouse | Commercient Prophet 21 Warehouse Managed Custom Object | Commercient external key (Epicor package) | 13 |
| Price book object | Price book object | External key (custom field) | 14 |
| Create Standard Price Book | Price book entry | External key (custom field) | 15 |
| Update Standard Pricebook | Price book entry | External key (custom field) | 16 |
| Custom Pricebook Create | Price book entry | External key (custom field) | 17 |
| Custom Pricebook Update | Price book entry | External key (custom field) | 18 |
| Product item Reverse lookup | Commercient Prophet 21 Inventory Master Managed Custom Object | Commercient external key (Epicor package) | 19 |
| Opportunity | Opportunity | External key (custom field) | 19 |
| Contacts | Contact | External key (custom field) | 20 |
| Opportunity line item | Opportunity line item | External key (custom field) | 20 |
| Quote | Quote | External key (custom field) | 22 |
| Quote Line | Quote line item | External key (custom field) | 23 |
| Standard Order | Order | External key (custom field) | 2510 |
| Standard order line | Order product | External key (custom field) | 2610 |
| Standard Order Update | Order | External key (custom field) | 2710 |
| Standard order line update | Order product | External key (custom field) | 2810 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| terms feed | insert + update | terms |
| salesperson feed | insert + update | customer salesperson assignments |
| CRM account feed | insert + update | customers, addresses, shipping addresses |
| customer feed | insert + update | customers, terms |
| shipping address feed | insert + update | shipping addresses, customers |
| a sales order feed under a mismatched name | insert only | order entry headers |
| a sales order line feed under a mismatched name | insert only | order entry lines |
| invoice feed | insert + update | invoice headers |
| invoice line feed | insert + update | invoice lines |
| customer account lookup feed | insert + update | customers |
| inventory master feed | insert + update | inventory items |
| product feed | insert + update | inventory items |
| warehouse feed | insert + update | inventory locations, inventory items, locations |
| price book feed | — | — |
| standard price book feed (new entries) | insert only | inventory items |
| standard price book feed (changes) | insert + update | inventory items |
| custom price book feed (new entries) | insert only | inventory items |
| custom price book feed (changes) | insert + update | inventory items |
| product item reverse lookup feed | insert + update | inventory items |
| opportunity feed | insert + update | quote headers, order entry headers |
| contact feed | insert only | contacts, customers, addresses |
| opportunity line feed | insert + update | quote lines, quote headers, inventory items |
| quote feed | insert only | quote headers, order entry headers, addresses |
| quote line feed | insert + update | quote lines, order entry lines, inventory items |
| standard order feed | insert + update | order entry headers, addresses, quote headers |
| standard order line feed | insert + update | order entry lines, inventory items |
| standard order feed (changes) | insert + update | order entry headers, addresses, quote headers |
| standard order line feed (changes) | insert + update | order entry lines, inventory items |

## 4. Order of work

The templates set run sequence from 1 to 2810. A run processes active rows in ascending run
sequence, which is the order the templates put them in:

- 1 — Terms code
- 2 — Salesperson
- 3 — Account
- 4 — Customer
- 5 — Ship to
- 6 — Order header
- 7 — Order line number
- 8 — Invoice header
- 9 — invoice line
- 10 — Customer Reverse Lookup Account
- 11 — Inv Master
- 12 — Product
- 13 — Warehouse
- 14 — Price book object
- 15 — Create Standard Price Book
- 16 — Update Standard Pricebook
- 17 — Custom Pricebook Create
- 18 — Custom Pricebook Update
- 19 — Product item Reverse lookup, Opportunity
- 20 — Contacts, Opportunity line item
- 22 — Quote
- 23 — Quote Line
- 2510 — Standard Order
- 2610 — Standard order line
- 2710 — Standard Order Update
- 2810 — Standard order line update

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- shipping address feed reads CRM account sync output, customer sync output
- product feed reads inventory master sync output (generic name)
- warehouse feed reads inventory master sync output (generic name), product record sync output
  (generic name)
- product item reverse lookup feed reads product record sync output (generic name), inventory master
  sync output (generic name)
- customer account lookup feed reads customer sync output
- contact feed reads CRM account sync output
- opportunity feed reads account sync output (generic name); no template in this set writes account
  sync output (generic name)
- opportunity line feed reads product record sync output (generic name), opportunity sync output
  (generic name)
- standard order feed reads account sync output (generic name), contact sync output (generic name),
  opportunity sync output (generic name), Salesforce quote sync output (generic name); no template
  in this set writes account sync output (generic name), contact sync output (generic name)
- standard order line feed reads standard order sync output, product record sync output (generic
  name), standard price book sync output (generic name), quote line sync output (generic name)
- standard order feed (changes) reads account sync output (generic name), contact sync output
  (generic name), opportunity sync output (generic name), Salesforce quote sync output (generic
  name); no template in this set writes account sync output (generic name), contact sync output
  (generic name)
- standard order line feed (changes) reads standard order sync output, product record sync output
  (generic name), standard price book sync output (generic name), quote line sync output (generic
  name)
- quote feed reads account sync output (generic name), opportunity sync output (generic name); no
  template in this set writes account sync output (generic name)
- quote line feed reads Salesforce quote sync output (generic name), product record sync output
  (generic name)
- customer feed reads CRM account sync output, salesperson sync output, terms sync output (generic
  name)
- invoice feed reads CRM account sync output, customer sync output, sales order sync output,
  salesperson sync output, terms sync output (generic name)
- invoice line feed reads invoice sync output
- standard price book feed (new entries) reads product record sync output (generic name)
- standard price book feed (changes) reads product record sync output (generic name)
- custom price book feed (new entries) reads product record sync output (generic name), price book
  sync output (generic name), price 3 sync output (generic name); no template in this set writes
  price 3 sync output (generic name)
- custom price book feed (changes) reads product record sync output (generic name), price book sync
  output (generic name), price 3 sync output (generic name); no template in this set writes price 3
  sync output (generic name)
- a sales order line feed under a mismatched name reads sales order sync output, a sales order line
  sync output under a mismatched name; no template in this set writes a sales order line sync output
  under a mismatched name
- a sales order feed under a mismatched name reads CRM account sync output, customer sync output,
  salesperson sync output (generic name), terms sync output (generic name), a sales order sync
  output under a mismatched name; no template in this set writes salesperson sync output (generic
  name), a sales order sync output under a mismatched name

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Terms code | Commercient Prophet 21 Terms Managed Custom Object | 28 | Terms identifier → Commercient external key (Epicor package), Terms identifier → Name, Billing cycle cutoff day → Commercient billing cycle cutoff day, Cash discount eligible → Commercient cash discount eligible, Credit card required → Commercient credit card required |
| Salesperson | Commercient Prophet 21 Salesperson Managed Custom Object | 13 | Company identifier, Customer salesperson identifier → Commercient external key (Epicor package), Company identifier, Customer salesperson identifier → Name, Commission percentage → Commercient commission percentage, Company identifier → Commercient company identifier (Prophet 21 package), Created by → Commercient created by |
| Account | Account | 15 | Company identifier,Customer identifier → Commercient AR customer code, Customer name → Name, Central phone number → Phone, Central fax number → Fax, Mailing address line 1,Mailing address line 2,Mailing address line 3 → Billing street |
| Customer | Commercient Prophet 21 Customer Managed Custom Object | 96 | Company identifier,Customer identifier → Commercient external key (Epicor package), Company identifier,Customer name → Name, Accept partial orders → Accept partial orders, Allow advance billing → Allow advance billing, Allowed account number → Allowed account number |
| Ship to | Commercient Prophet 21 Ship To Address Managed Custom Object | 91 | Company identifier, Ship to identifier → Commercient external key (Epicor package), Customer name → Name, Acceptable wait time → Acceptable wait time, Accept partial orders → Accept partial orders, Alternate tax rate eligible → Alternate tax rate eligible |
| Order header | Commercient Prophet 21 Sales Order Header Managed Custom Object | 87 | Company identifier, Order number → Commercient external key (Epicor package), Company identifier, Order number → Name, Address identifier → Address identifier, Approved → Approved, Architect identifier → Architect identifier |
| Order line number | Commercient Prophet 21 Sales Order Detail Managed Custom Object | 97 | Company number, Line number, Order number → Commercient external key (Epicor package), Company number, Line number, Order number → Name, Allocate usage to original item → Allocate usage to original item, Assembly → Assembly, Base unit price → Base unit price |
| Invoice header | Commercient Prophet 21 Invoice Header Managed Custom Object | 96 | Company number,Invoice number → Commercient external key (Epicor package), Company number,Invoice number → Name, allowed → allowed, Amount paid → Amount paid, Approved → Approved |
| invoice line | Commercient Prophet 21 Invoice Line Managed Custom Object | 79 | Company identifier,Invoice number,Line number → Commercient external key (Epicor package), Company identifier,Invoice number,Line number → Name, Administration fee → Administration fee, Budget code → Budget code, Buyer → Buyer |
| Customer Reverse Lookup Account | Account | 2 | Company identifier, Customer identifier → Commercient AR customer code, the linked Salesforce record → Commercient Epicor Customer Managed Custom Object |
| Inv Master | Commercient Prophet 21 Inventory Master Managed Custom Object | 91 | Inventory master identifier,Item identifier → Commercient external key (Epicor package), Inventory master identifier,Item identifier → Name, Attribute group identifier → Attribute group identifier, Available for scheduled delivery → Available for scheduled delivery, Base unit → Base unit |
| Product | Product | 6 | Inventory master identifier, Item identifier → Commercient external key (earlier package), Item identifier, Item description → Name, inactive → Active, Item identifier → Product code, Item description → Description |
| Warehouse | Commercient Prophet 21 Warehouse Managed Custom Object | 93 | Company identifier, Inventory master identifier, Location identifier → Commercient external key (Epicor package), Location name, Location identifier → Name, Allow direct ship of discontinued items → Allow direct ship of discontinued items, Allow special order of discontinued items → Allow special order of discontinued items, Alternate tax group identifier → Alternate tax group identifier |
| Price book object | Price book object | 3 | External key (custom field) → External key (custom field), Name → Name, Row timestamp → Row timestamp |
| Create Standard Price Book | Price book entry | 6 | the linked Salesforce record → product lookup, the linked Salesforce record → price book lookup, Active → Active, Unit price → Unit price, Inventory master identifier, Item identifier → External key (custom field) |
| Update Standard Pricebook | Price book entry | 4 | Active → Active, Unit price → Unit price, Inventory master identifier, Item identifier → External key (custom field), → Row timestamp |
| Custom Pricebook Create | Price book entry | 6 | the linked Salesforce record → product lookup, the linked Salesforce record → price book lookup, Active → Active, price 3 sync output (generic name) → Unit price, Inventory master identifier, Item identifier → External key (custom field) |
| Custom Pricebook Update | Price book entry | 4 | price 3 sync output (generic name) → Active, price 3 sync output (generic name) → Unit price, Inventory master identifier, Item identifier → External key (custom field), → Row timestamp |
| Product item Reverse lookup | Commercient Prophet 21 Inventory Master Managed Custom Object | 2 | Inventory master identifier, Item identifier → Commercient external key (Epicor package), the linked Salesforce record → Commercient product (related record) |
| Opportunity | Opportunity | 6 | Quote header identifier → External key (custom field), Company identifier, Customer identifier, Address identifier → account lookup, Quote header identifier → Name, Expiration date → close date property |
| Contacts | Contact | 11 | id, Address identifier → External key (custom field), the linked Salesforce record → account lookup, id → Prophet 21 contact identifier (custom field), First name → Given name, Last name → Family name |
| Opportunity line item | Opportunity line item | 6 | the linked Salesforce record → product lookup, the linked Salesforce record → Opportunity, the linked Salesforce record → price book entry lookup, Quote line identifier → external key column, Price 1 → Unit price |
| Quote | Quote | 21 | Order number → External key (custom field), Order number → Name, the linked Salesforce record → Opportunity, the linked Salesforce record → account lookup, price book lookup → price book lookup |
| Quote Line | Quote line item | 6 | Order number, Line number → External key (custom field), Inventory master identifier, Item identifier → product lookup, Quantity ordered → Quantity, Unit price → Unit price, Inventory master identifier, Item identifier → price book entry lookup |
| Standard Order | Order | 28 | Company identifier, Order number → External key (custom field), Customer identifier → account lookup, Contact identifier → bill to contact lookup, Contact identifier → ship to contact lookup, Order header identifier → Opportunity |
| Standard order line | Order product | 12 | Company number, Line number, Order number → External key (custom field), Order number → Order identifier, Inventory master identifier, Item identifier → product lookup, Inventory master identifier, Item identifier → price book entry lookup, Order number, Line number → quote line item lookup |
| Standard Order Update | Order | 26 | Company identifier, Order number → External key (custom field), Customer identifier → account lookup, Contact identifier → bill to contact lookup, Contact identifier → ship to contact lookup, Quote header identifier → Opportunity |
| Standard order line update | Order product | 10 | Company number, Line number, Order number → External key (custom field), Order number → Order identifier, Order number, Line number → quote line item lookup, Extended description → Description, Required date → End date |

## 6. Community templates

The catalogue carries 637 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 637
- Default operations: insert on 637, update on 637, delete on 637
- Marked as circular sync: 4
- Licence groups they span: 17
- Destination objects: Account, Commercient Prophet 21 Inventory Master Managed Custom Object, Price
  book entry, Commercient Prophet 21 Customer Managed Custom Object, Contact, Commercient Prophet 21
  Sales Order Header Managed Custom Object, Commercient Prophet 21 Ship To Address Managed Custom
  Object, Commercient Prophet 21 Invoice Header Managed Custom Object, Commercient Prophet 21
  Invoice Line Managed Custom Object, Product, Commercient Prophet 21 Sales Order Detail Managed
  Custom Object, Commercient Prophet 21 Terms Managed Custom Object, Commercient Prophet 21
  Salesperson Managed Custom Object, Order product, User, Opportunity, Commercient Prophet 21
  Warehouse Managed Custom Object, Opportunity line item, Commercient Prophet 21 Address Managed
  Custom Object, 39 more and 19 custom objects
- Object display names: Account, Customer Reverse Lookup Account, Ship to, Contacts, Customer,
  invoice line, Invoice header, Product, Order header, Salesperson, Terms code, Inv Master, 125 more
  and 30 further templates
- Template groups: Account, Product, Invoice, Sales order, Customer Multi Ship Addresses, CRM Order
  and Line, CRM Opportunity and Line, CRM Quote and Line, Opportunity, Pricebook, Vendor

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/epicor-prophet-21-p21`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Epicor Prophet 21 (Prophet 21) → Salesforce templates set up. dlake-crmpro-salesforce is
the destination skill this page sits under: its own text is the authority for the Salesforce
conventions that hold across every ERP, and its ERP table lists this page alongside every sibling
ERP page for this destination. For the extract leg that fills the source data, see dlake-normalsync;
for the on-premises agent that runs it, dlake-syncagent; for the writeback leg,
dlake-txdownloaderpro; for standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/epicor-prophet-21-p21`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
