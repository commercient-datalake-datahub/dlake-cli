---
name: dlake-crmpro-zohocrm/erps/epicor-prophet-21-p21
kind: erp-summary
description: >-
  Use it when standing up or reading an Epicor Prophet 21 (Prophet 21) → Zoho CRM template set, when
  deciding which templates to import and activate, or when a run completes without pushing records
  and the answer is in the view or the configuration row. It extends dlake-crmpro, which covers
  operating CRMPro generally, and dlake-crmpro-zohocrm, the destination skill this page is a child
  of, which carries the Zoho CRM conventions that hold across every ERP.
---
# CRMPro → Zoho CRM — Epicor Prophet 21 (Prophet 21): what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/epicor-prophet-21-p21` (or `list_skills`) against
the Commercient admin plane. Existing customers who need access or help: contact
support@commercient.com. New customers: contact sales@commercient.com to become a customer and be
whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-zohocrm is the
destination skill this page is a child of, and the authority for the Zoho CRM conventions that hold
across every ERP: read it first, then come back here for what this source's own templates set. This
page grows as the catalogue does.

## 1. What the templates deliver

| Group | Business outcome | Objects | Source tables and views |
|---|---|---|---|
| **Epicor Prophet 21 Terms** | The templates push Commercient Epicor Prophet 21 terms object to Zoho CRM. | Commercient Epicor Prophet 21 terms object | terms |
| **Epicor Prophet 21 Branch** | The templates push Commercient Epicor Prophet 21 branch object to Zoho CRM. | Commercient Epicor Prophet 21 branch object | branches |
| **Epicor Prophet 21 Class** | The templates push Commercient Epicor Prophet 21 class object to Zoho CRM. | Commercient Epicor Prophet 21 class object | classes |
| **Epicor Prophet 21 Territory** | The templates push Commercient Epicor Prophet 21 territory object to Zoho CRM. | Commercient Epicor Prophet 21 territory object | territories |
| **Epicor Prophet 21 Sales Person** | The templates push Commercient Epicor Prophet 21 salesperson object to Zoho CRM. | Commercient Epicor Prophet 21 salesperson object | contacts |
| **Accounts** | The templates push Accounts to Zoho CRM. | Accounts | customers, addresses, shipping addresses |
| **Epicor Prophet 21 Customer** | The templates push Commercient Epicor Prophet 21 customer object to Zoho CRM. | Commercient Epicor Prophet 21 customer object | customers, terms |
| **Epicor Prophet 21 Ship To** | The templates push Commercient Epicor Prophet 21 shipping address object to Zoho CRM. | Commercient Epicor Prophet 21 shipping address object | shipping addresses |
| **Contacts** | The templates push Contacts to Zoho CRM. | Contacts | contacts, order entry customer contacts, addresses |
| **Epicor Prophet 21 Address** | The templates push Commercient Epicor Prophet 21 address object to Zoho CRM. | Commercient Epicor Prophet 21 address object | addresses, shipping addresses |
| **Products** | The templates push Products to Zoho CRM. | Products | inventory items |
| **Price books** | The templates push Zoho price books to Zoho CRM. | Zoho price books | price book records |
| **Product Price books** | The templates push Zoho product price book relation to Zoho CRM. | Zoho product price book relation | inventory items |
| **Epicor Prophet 21 Item Master** | The templates push Commercient Epicor Prophet 21 item master object to Zoho CRM. | Commercient Epicor Prophet 21 item master object | inventory items |
| **Epicor Prophet 21 Warehouse** | The templates push Commercient Epicor Prophet 21 warehouse object to Zoho CRM. | Commercient Epicor Prophet 21 warehouse object | locations |
| **Epicor Prophet 21 Item Warehouse** | The templates push Commercient Epicor Prophet 21 item warehouse object to Zoho CRM. | Commercient Epicor Prophet 21 item warehouse object | inventory locations, inventory items |
| **Epicor Prophet 21 Serial Number** | The templates push Commercient Epicor Prophet 21 serial number object to Zoho CRM. | Commercient Epicor Prophet 21 serial number object | serial numbers, inventory items |
| **Quotes** | The templates push Quotes to Zoho CRM. | Quotes | quote headers, order entry headers, addresses, shipping addresses, order entry lines, inventory items |
| **Epicor Prophet 21 Sales order header** | The templates push Commercient Epicor Prophet 21 sales order header object to Zoho CRM. | Commercient Epicor Prophet 21 sales order header object | order entry headers, quote headers |
| **Epicor Prophet 21 Sales order detail** | The templates push Commercient Epicor Prophet 21 sales order detail object to Zoho CRM. | Commercient Epicor Prophet 21 sales order detail object | order entry lines, suppliers, inventory items |
| **Epicor Prophet 21 Invoice Header** | The templates push Commercient Epicor Prophet 21 invoice header object to Zoho CRM. | Commercient Epicor Prophet 21 invoice header object | invoice headers |
| **Epicor Prophet 21 Invoice Line** | The templates push Commercient Epicor Prophet 21 invoice detail object to Zoho CRM. | Commercient Epicor Prophet 21 invoice detail object | invoice lines, inventory items |
| **Epicor Prophet 21 Invoice payment** | The templates push Commercient Epicor Prophet 21 invoice payment object to Zoho CRM. | Commercient Epicor Prophet 21 invoice payment object | AR receipt details |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Epicor Prophet 21 Terms | Commercient Epicor Prophet 21 terms object | Commercient external key column | 1 |
| Epicor Prophet 21 Branch | Commercient Epicor Prophet 21 branch object | Commercient external key column | 2 |
| Epicor Prophet 21 Class | Commercient Epicor Prophet 21 class object | Commercient external key column | 3 |
| Epicor Prophet 21 Territory | Commercient Epicor Prophet 21 territory object | Commercient external key column | 4 |
| Epicor Prophet 21 Sales Person | Commercient Epicor Prophet 21 salesperson object | Commercient external key column | 5 |
| Accounts | Accounts | Commercient external key column | 6 |
| Epicor Prophet 21 Customer | Commercient Epicor Prophet 21 customer object | Commercient external key column | 7 |
| Epicor Prophet 21 Ship To | Commercient Epicor Prophet 21 shipping address object | Commercient external key column | 8 |
| Contacts | Contacts | Commercient external key column | 9 |
| Epicor Prophet 21 Address | Commercient Epicor Prophet 21 address object | Commercient external key column | 10 |
| Products | Products | Commercient external key column | 11 |
| Price books | Zoho price books | Commercient external key column | 12 |
| Product Price books | Zoho product price book relation | Commercient external key column | 13 |
| Epicor Prophet 21 Item Master | Commercient Epicor Prophet 21 item master object | Commercient external key column | 14 |
| Epicor Prophet 21 Warehouse | Commercient Epicor Prophet 21 warehouse object | Commercient external key column | 15 |
| Epicor Prophet 21 Item Warehouse | Commercient Epicor Prophet 21 item warehouse object | Commercient external key column | 16 |
| Epicor Prophet 21 Serial Number | Commercient Epicor Prophet 21 serial number object | Commercient external key column | 17 |
| Quotes | Quotes | Commercient external key column | 18 |
| Epicor Prophet 21 Sales order header | Commercient Epicor Prophet 21 sales order header object | Commercient external key column | 19 |
| Epicor Prophet 21 Sales order detail | Commercient Epicor Prophet 21 sales order detail object | Commercient external key column | 20 |
| Epicor Prophet 21 Invoice Header | Commercient Epicor Prophet 21 invoice header object | Commercient external key column | 21 |
| Epicor Prophet 21 Invoice Line | Commercient Epicor Prophet 21 invoice detail object | Commercient external key column | 22 |
| Epicor Prophet 21 Invoice payment | Commercient Epicor Prophet 21 invoice payment object | Commercient external key column | 23 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| terms feed | insert only | terms |
| branch feed | insert only | branches |
| class feed | insert only | classes |
| territory feed | insert only | territories |
| salesperson feed | insert only | contacts |
| account feed | insert only | customers, addresses, shipping addresses |
| customer feed | insert only | customers, terms |
| shipping address feed | insert only | shipping addresses |
| contact feed | insert + update | contacts, order entry customer contacts, addresses |
| address feed | insert only | addresses, shipping addresses |
| product feed | insert + update | inventory items |
| price book feed (generic name) | insert + update | price book records |
| product price list feed | insert + update | inventory items |
| item master feed | insert + update | inventory items |
| warehouse feed | insert + update | locations |
| item warehouse feed | insert + update | inventory locations, inventory items |
| serial number feed | insert only | serial numbers, inventory items |
| quote feed | insert only | quote headers, order entry headers, addresses, shipping addresses, order entry lines |
| sales order feed | insert only | order entry headers, quote headers |
| sales order line feed | insert only | order entry lines, suppliers, inventory items |
| invoice feed | insert only | invoice headers |
| invoice line feed | insert only | invoice lines, inventory items |
| invoice payment feed | insert only | AR receipt details |

## 4. Order of work

The templates set run sequence from 1 to 23. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Epicor Prophet 21 Terms
- 2 — Epicor Prophet 21 Branch
- 3 — Epicor Prophet 21 Class
- 4 — Epicor Prophet 21 Territory
- 5 — Epicor Prophet 21 Sales Person
- 6 — Accounts
- 7 — Epicor Prophet 21 Customer
- 8 — Epicor Prophet 21 Ship To
- 9 — Contacts
- 10 — Epicor Prophet 21 Address
- 11 — Products
- 12 — Price books
- 13 — Product Price books
- 14 — Epicor Prophet 21 Item Master
- 15 — Epicor Prophet 21 Warehouse
- 16 — Epicor Prophet 21 Item Warehouse
- 17 — Epicor Prophet 21 Serial Number
- 18 — Quotes
- 19 — Epicor Prophet 21 Sales order header
- 20 — Epicor Prophet 21 Sales order detail
- 21 — Epicor Prophet 21 Invoice Header
- 22 — Epicor Prophet 21 Invoice Line
- 23 — Epicor Prophet 21 Invoice payment

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- terms feed reads terms sync output (generic name); no template in this set writes terms sync
  output (generic name)
- branch feed reads address sync output (generic name), branch sync output (generic name); no
  template in this set writes address sync output (generic name), branch sync output (generic name)
- class feed reads class sync output (generic name); no template in this set writes class sync
  output (generic name)
- territory feed reads territory sync output (generic name); no template in this set writes
  territory sync output (generic name)
- salesperson feed reads address sync output (generic name), territory sync output (generic name),
  branch sync output (generic name), salesperson sync output (generic name); no template in this set
  writes address sync output (generic name), territory sync output (generic name), branch sync
  output (generic name), salesperson sync output (generic name)
- account feed reads salesperson sync output (generic name), terms sync output (generic name),
  account sync output (generic name); no template in this set writes salesperson sync output
  (generic name), terms sync output (generic name), account sync output (generic name)
- customer feed reads account sync output (generic name), salesperson sync output (generic name),
  terms sync output (generic name), customer sync output (generic short name); no template in this
  set writes account sync output (generic name), salesperson sync output (generic name), terms sync
  output (generic name), customer sync output (generic short name)
- shipping address feed reads account sync output (generic name), customer sync output (generic
  short name), shipping address sync output (generic name); no template in this set writes account
  sync output (generic name), customer sync output (generic short name), shipping address sync
  output (generic name)
- contact feed reads account sync output (generic name), contact sync output (generic name); no
  template in this set writes account sync output (generic name), contact sync output (generic name)
- address feed reads shipping address sync output (generic name), account sync output (generic
  name), customer sync output (generic short name), address sync output (generic name); no template
  in this set writes shipping address sync output (generic name), account sync output (generic
  name), customer sync output (generic short name), address sync output (generic name)
- product feed reads inventory master sync output (generic name), product record sync output
  (generic name); no template in this set writes inventory master sync output (generic name),
  product record sync output (generic name)
- price book feed (generic name) reads price book sync output (generic name); no template in this
  set writes price book sync output (generic name)
- product price list feed reads price book sync output (generic name), product record sync output
  (generic name), product price list sync output (generic name); no template in this set writes
  price book sync output (generic name), product record sync output (generic name), product price
  list sync output (generic name)
- item master feed reads product record sync output (generic name), item master sync output (generic
  name); no template in this set writes product record sync output (generic name), item master sync
  output (generic name)
- warehouse feed reads warehouse sync output (generic name); no template in this set writes
  warehouse sync output (generic name)
- item warehouse feed reads item master sync output (generic name), product record sync output
  (generic name), warehouse sync output (generic name), item warehouse sync output (generic name);
  no template in this set writes item master sync output (generic name), product record sync output
  (generic name), warehouse sync output (generic name), item warehouse sync output (generic name)
- serial number feed reads warehouse sync output (generic name), item master sync output (generic
  name), product record sync output (generic name), serial number sync output (generic name); no
  template in this set writes warehouse sync output (generic name), item master sync output (generic
  name), product record sync output (generic name), serial number sync output (generic name)
- quote feed reads account sync output (generic name), product record sync output (generic name),
  quote sync output (generic name); no template in this set writes account sync output (generic
  name), product record sync output (generic name), quote sync output (generic name)
- sales order feed reads account sync output (generic name), customer sync output (generic short
  name), quote sync output (generic name), sales order header sync output (generic name); no
  template in this set writes account sync output (generic name), customer sync output (generic
  short name), quote sync output (generic name), sales order header sync output (generic name)
- sales order line feed reads sales order header sync output (generic name), item master sync output
  (generic name), product record sync output (generic name), sales order line sync output (generic
  name); no template in this set writes sales order header sync output (generic name), item master
  sync output (generic name), product record sync output (generic name), sales order line sync
  output (generic name)
- invoice feed reads account sync output (generic name), customer sync output (generic short name),
  sales order header sync output (generic name), invoice header sync output (generic name); no
  template in this set writes account sync output (generic name), customer sync output (generic
  short name), sales order header sync output (generic name), invoice header sync output (generic
  name)
- invoice line feed reads invoice header sync output (generic name), item master sync output
  (generic name), product record sync output (generic name), invoice line sync output (generic
  name); no template in this set writes invoice header sync output (generic name), item master sync
  output (generic name), product record sync output (generic name), invoice line sync output
  (generic name)
- invoice payment feed reads account sync output (generic name), customer sync output (generic short
  name), invoice header sync output (generic name), invoice payment sync output (generic name); no
  template in this set writes account sync output (generic name), customer sync output (generic
  short name), invoice header sync output (generic name), invoice payment sync output (generic name)

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Zoho CRM pairs |
|---|---|---|---|
| Epicor Prophet 21 Terms | Commercient Epicor Prophet 21 terms object | 29 | Terms description → Name, Terms identifier → external key column, Terms identifier → Commercient external key (custom field), Billing cycle cutoff day → Billing cycle cutoff day, Cash discount eligible → Cash discount eligible |
| Epicor Prophet 21 Branch | Commercient Epicor Prophet 21 branch object | 20 | Company identifier, Branch identifier → Commercient external key (custom field), Branch description → Branch description, Branch description → Name, Branch identifier → Branch identifier, Branch unique identifier → Branch unique identifier |
| Epicor Prophet 21 Class | Commercient Epicor Prophet 21 class object | 19 | Class type, Class number, Class identifier → Commercient external key (custom field), Affinity flag → Affinity flag, Available for cycle count flag → Available for cycle count flag, Class description → Class description, Class description → Name |
| Epicor Prophet 21 Territory | Commercient Epicor Prophet 21 territory object | 10 | Territory unique identifier → Commercient external key (custom field), Territory unique identifier → external key column, Created by → Created by, Territory description → Name, Date created → Date created |
| Epicor Prophet 21 Sales Person | Commercient Epicor Prophet 21 salesperson object | 69 | id → Commercient external key (custom field), id → Name, Address name → Address name, Ads user → Ads user, Beeper → Beeper |
| Accounts | Accounts | 16 | Customer identifier → Commercient external key (custom field), Customer name → Account name, Central phone number → Phone, Central fax number → Fax, Mailing address line 1, Mailing address line 2 → Billing street (Zoho field) |
| Epicor Prophet 21 Customer | Commercient Epicor Prophet 21 customer object | 258 | Customer identifier → external key column, Customer identifier → Commercient external key (custom field), Terms identifier → Terms identifier, Duplicate PO number → Duplicate PO number, Exclude cancelled from order acknowledgement → Exclude cancelled from order acknowledgement |
| Epicor Prophet 21 Ship To | Commercient Epicor Prophet 21 shipping address object | 132 | Company identifier, Ship to identifier → Commercient external key (custom field), Ship to identifier → Name, Accept partial orders → Accept partial orders, Freight mileage amount → Freight mileage amount, Other exemption number → Other exemption number |
| Contacts | Contacts | 19 | Customer identifier → Account (custom field), id → external key column, id → Commercient external key (custom field), Last name → Last name, First name → First name |
| Epicor Prophet 21 Address | Commercient Epicor Prophet 21 address object | 108 | id → external key column, id → Commercient external key (custom field), name, id → Name, Longitude → Longitude, Less than truckload freight calculation percentage → Less than truckload freight calculation percentage |
| Products | Products | 7 | Inventory master identifier, Item identifier → Commercient external key (custom field), Item identifier, Item description → Product name (Zoho field), inactive → Product active (Zoho field), VAT taxable flag → Taxable (Zoho field), Item identifier → ERP product code |
| Price books | Zoho price books | 4 | Price book unique identifier → Commercient external key (custom field), Price book identifier → Price book name (Zoho field), Active → Active, description → Description |
| Product Price books | Zoho product price book relation | 7 | Inventory master identifier, Item identifier → Commercient external key column, Price 1 → List price, the linked Salesforce record → Price book lookup (Zoho field), the linked Salesforce record → Product lookup (Zoho field), Price book name → Price book name |
| Epicor Prophet 21 Item Master | Commercient Epicor Prophet 21 item master object | 143 | Inventory master identifier,Item identifier → Commercient external key (custom field), Inventory master identifier,Item identifier → Name, Aia enabled flag → Aia enabled flag, Aia remnant quantity → Commercient aia remnant quantity, Allow custom description flag → Allow custom description flag |
| Epicor Prophet 21 Warehouse | Commercient Epicor Prophet 21 warehouse object | 93 | Location identifier → Commercient external key (custom field), Location name → Name, Adjust found items flag → Adjust found items flag, Allow multiple assemblies flag → Allow multiple assemblies flag, Associated location identifier → Associated location identifier |
| Epicor Prophet 21 Item Warehouse | Commercient Epicor Prophet 21 item warehouse object | 131 | Company identifier, Inventory master identifier, Location identifier → Commercient external key (custom field), Company identifier, Inventory master identifier, Location identifier → Name, Inventory master identifier → Commercient Epicor Prophet 21 item (related record), Location identifier → Commercient Epicor Prophet 21 warehouse (related record), Inventory master identifier → Products |
| Epicor Prophet 21 Serial Number | Commercient Epicor Prophet 21 serial number object | 20 | Company number, Location identifier, Serial number (Zoho field), Inventory master identifier → Commercient external key (custom field), Company number, Location identifier, Serial number (Zoho field), Inventory master identifier → Name, Allowance amount → Allowance amount, Allowance amount modified date → Allowance amount modified date, Bin identifier → Bin identifier |
| Quotes | Quotes | 18 | Quote header identifier → Commercient external key (custom field), Customer identifier → Account name, Order number → Name, Order number → returned quote number, Date created → ERP created date (custom field) |
| Epicor Prophet 21 Sales order header | Commercient Epicor Prophet 21 sales order header object | 197 | Order number → external key column, Order number → Commercient external key (custom field), Order number → Name, Free on board flag → Free on board flag, url → Url |
| Epicor Prophet 21 Sales order detail | Commercient Epicor Prophet 21 sales order detail object | 182 | Line number,Order number → external key column, Line number,Order number → Commercient external key (custom field), Order number → Order number, Tag hold class unique identifier → Tag hold class unique identifier, Allocate usage to original item → Commercient allocated usage to original item |
| Epicor Prophet 21 Invoice Header | Commercient Epicor Prophet 21 invoice header object | 133 | Invoice number → external key column, Invoice number → Commercient external key (custom field), Order date → Order date, Delivery number → Delivery number, Electronic data interchange order printed flag → Electronic data interchange order printed flag |
| Epicor Prophet 21 Invoice Line | Commercient Epicor Prophet 21 invoice detail object | 87 | Invoice number,Line number → Commercient external key (custom field), Unit of measure → Unit of measure, Item identifier → Item identifier, Item description → Item description, general ledger revenue account number → general ledger revenue account number |
| Epicor Prophet 21 Invoice payment | Commercient Epicor Prophet 21 invoice payment object | 22 | Invoice number, Receipt number → Commercient external key (custom field), Invoice number, Receipt number → Name, Invoice number, Receipt number → external key column, Invoice number → Invoice number, Company identifier → Company identifier |

## 6. Community templates

The catalogue carries 37 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 37
- Default operations: insert on 37, update on 37, delete on 37
- Marked as circular sync: 0
- Licence groups they span: 4
- Destination objects: Accounts, Commercient Epicor Prophet 21 address object, Commercient Epicor
  Prophet 21 customer object, Commercient Epicor Prophet 21 invoice detail object, Commercient
  Epicor Prophet 21 invoice header object, Commercient Epicor Prophet 21 invoice payment object,
  Commercient Epicor Prophet 21 salesperson object, Commercient Epicor Prophet 21 shipping address
  object, Commercient Epicor Prophet 21 sales order detail object, Commercient Epicor Prophet 21
  sales order header object, Contacts, Quotes, Commercient Epicor Prophet 21 branch object,
  Commercient Epicor Prophet 21 class object, Commercient Epicor Prophet 21 item master object,
  Commercient Epicor Prophet 21 item warehouse object, Commercient Epicor Prophet 21 serial number
  object, Commercient Epicor Prophet 21 terms object, Commercient Epicor Prophet 21 territory
  object, Commercient Epicor Prophet 21 warehouse object, 3 more and a custom object
- Object display names: Accounts, Contacts, Epicor Prophet 21 Address, Epicor Prophet 21 Invoice
  Header, Epicor Prophet 21 Invoice payment, Epicor Prophet 21 Sales Person, Epicor Prophet 21 Ship
  To, Epicor Prophet 21 Branch, Epicor Prophet 21 Class, Epicor Prophet 21 Customer, Epicor Prophet
  21 Invoice Line, 16 more and a further template
- Template groups: Account, CRM Quote and Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/epicor-prophet-21-p21`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Epicor Prophet 21 (Prophet 21) → Zoho CRM templates set up. dlake-crmpro-zohocrm is the
destination skill this page sits under: its own text is the authority for the Zoho CRM conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-zohocrm/erps/epicor-prophet-21-p21`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
