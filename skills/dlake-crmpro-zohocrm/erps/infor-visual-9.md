---
name: dlake-crmpro-zohocrm/erps/infor-visual-9
kind: erp-summary
description: >-
  Use it when standing up or reading an Infor Visual 9 → Zoho CRM template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-zohocrm, the destination skill this page is a child of, which carries
  the Zoho CRM conventions that hold across every ERP.
---
# CRMPro → Zoho CRM — Infor Visual 9: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/infor-visual-9` (or `list_skills`) against the
Commercient admin plane. Existing customers who need access or help: contact
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
| **Infor Visual Terms** | The templates push Commercient Infor Visual terms object to Zoho CRM. | Commercient Infor Visual terms object | terms |
| **Infor Visual Salesperson** | The templates push Commercient Infor Visual salesperson object to Zoho CRM. | Commercient Infor Visual salesperson object | sales reps |
| **Account** | The templates push Accounts to Zoho CRM. | Accounts | customers |
| **Infor Visual Customer** | The templates push Commercient Infor Visual customer object to Zoho CRM. | Commercient Infor Visual customer object | customers |
| **Customer Reverse Lookup Account** | The templates push Accounts to Zoho CRM. | Accounts | customers |
| **Infor Visual Customer Address** | The templates push Commercient Infor Visual address object to Zoho CRM. | Commercient Infor Visual address object | customer addresses |
| **Product** | The templates push Products to Zoho CRM. | Products | parts |
| **Infor Visual Part** | The templates push Commercient Infor Visual part object to Zoho CRM. | Commercient Infor Visual part object | parts, part binary data |
| **Infor Visual Order** | The templates push Commercient Infor Visual order object to Zoho CRM. | Commercient Infor Visual order object | customer orders |
| **Infor Visual Order Line** | The templates push Commercient Infor Visual order line object to Zoho CRM. | Commercient Infor Visual order line object | customer order lines |
| **Infor Visual Receivable** | The templates push Commercient Infor Visual receivable object to Zoho CRM. | Commercient Infor Visual receivable object | receivables |
| **Infor Visual Receivable Line** | The templates push Commercient Infor Visual receivable line object to Zoho CRM. | Commercient Infor Visual receivable line object | receivable lines |
| **Infor Visual Quote** | The templates push Commercient Infor Visual quote object to Zoho CRM. | Commercient Infor Visual quote object | quotes |
| **Infor Visual Quote Line** | The templates push Commercient Infor Visual quote line object to Zoho CRM. | Commercient Infor Visual quote line object | quote lines |
| **Infor Visual Quote Price** | The templates push Commercient Infor Visual Quote Price (Zoho package object) to Zoho CRM. | Commercient Infor Visual Quote Price (Zoho package object) | quote prices |
| **Contact** | The templates push Contacts to Zoho CRM. | Contacts | customer contacts |
| **Account Notes** | The templates push Infor Visual notes (custom object) to Zoho CRM. | Infor Visual notes (custom object) | notations |
| **Order Notes** | The templates push Infor Visual notes (custom object) to Zoho CRM. | Infor Visual notes (custom object) | notations |
| **Quote Notes** | The templates push Infor Visual notes (custom object) to Zoho CRM. | Infor Visual notes (custom object) | notations |
| **Quotes** | The templates push Quotes to Zoho CRM. | Quotes | quote prices, quote lines, quotes, customers, customer addresses, x |
| **Sales orders** | The templates push Sales orders to Zoho CRM. | Sales orders | customer order lines, customer orders, customers, customer addresses, x |
| **Invoices** | The templates push Invoices to Zoho CRM. | Invoices | receivable lines, receivables, customers, customer addresses, x |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Infor Visual Terms | Commercient Infor Visual terms object | Commercient external key column | 1 |
| Infor Visual Salesperson | Commercient Infor Visual salesperson object | Commercient external key column | 2 |
| Account | Accounts | Commercient AR customer code (Zoho field) | 3 |
| Infor Visual Customer | Commercient Infor Visual customer object | Commercient external key column | 4 |
| Customer Reverse Lookup Account | Accounts | Commercient AR customer code (Zoho field) | 5 |
| Infor Visual Customer Address | Commercient Infor Visual address object | Commercient external key column | 6 |
| Product | Products | Commercient external key column | 7 |
| Infor Visual Part | Commercient Infor Visual part object | Commercient external key column | 8 |
| Infor Visual Order | Commercient Infor Visual order object | Commercient external key column | 9 |
| Infor Visual Order Line | Commercient Infor Visual order line object | Commercient external key column | 10 |
| Infor Visual Receivable | Commercient Infor Visual receivable object | Commercient external key column | 11 |
| Infor Visual Receivable Line | Commercient Infor Visual receivable line object | Commercient external key column | 12 |
| Infor Visual Quote | Commercient Infor Visual quote object | Commercient external key column | 13 |
| Infor Visual Quote Line | Commercient Infor Visual quote line object | Commercient external key column | 14 |
| Infor Visual Quote Price | Commercient Infor Visual Quote Price (Zoho package object) | Commercient external key column | 15 |
| Contact | Contacts | Commercient external key column | 16 |
| Account Notes | Infor Visual notes (custom object) | Commercient external key column | 17 |
| Order Notes | Infor Visual notes (custom object) | Commercient external key column | 18 |
| Quote Notes | Infor Visual notes (custom object) | Commercient external key column | 19 |
| Quotes | Quotes | Commercient external key column | 20 |
| Sales orders | Sales orders | Commercient external key column | 21 |
| Invoices | Invoices | Commercient external key column | 22 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| terms feed | insert only | terms |
| salesperson feed | insert only | sales reps |
| account feed | insert + update | customers |
| customer feed | insert only | customers |
| customer account lookup feed | insert + update | customers |
| customer address feed | insert only | customer addresses |
| Infor Visual 9 product feed | insert only | parts |
| Infor Visual 9 part feed | insert only | parts, part binary data |
| order feed | insert only | customer orders |
| order line feed | insert only | customer order lines |
| receivable feed | insert only | receivables |
| receivable line feed | insert only | receivable lines |
| quote feed | insert only | quotes |
| quote line feed | insert only | quote lines |
| quote price feed | insert only | quote prices |
| contact feed | insert only | customer contacts |
| account note feed | insert + update | notations |
| order note feed | insert + update | notations |
| quote note feed | insert + update | notations |
| Zoho quote feed | insert only | quote prices, quote lines, quotes, customers, customer addresses |
| Zoho sales order feed | insert only | customer order lines, customer orders, customers, customer addresses, x |
| Zoho invoice feed | insert only | receivable lines, receivables, customers, customer addresses, x |

## 4. Order of work

The templates set run sequence from 1 to 22. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Infor Visual Terms
- 2 — Infor Visual Salesperson
- 3 — Account
- 4 — Infor Visual Customer
- 5 — Customer Reverse Lookup Account
- 6 — Infor Visual Customer Address
- 7 — Product
- 8 — Infor Visual Part
- 9 — Infor Visual Order
- 10 — Infor Visual Order Line
- 11 — Infor Visual Receivable
- 12 — Infor Visual Receivable Line
- 13 — Infor Visual Quote
- 14 — Infor Visual Quote Line
- 15 — Infor Visual Quote Price
- 16 — Contact
- 17 — Account Notes
- 18 — Order Notes
- 19 — Quote Notes
- 20 — Quotes
- 21 — Sales orders
- 22 — Invoices

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- terms feed reads terms sync output; no template in this set writes terms sync output
- salesperson feed reads salesperson sync output; no template in this set writes salesperson sync
  output
- account feed reads salesperson sync output, account sync output; no template in this set writes
  salesperson sync output, account sync output
- customer feed reads account sync output, customer sync output; no template in this set writes
  account sync output, customer sync output
- customer account lookup feed reads customer sync output, customer account lookup sync output; no
  template in this set writes customer sync output, customer account lookup sync output
- customer address feed reads account sync output, customer sync output, customer address sync
  output; no template in this set writes account sync output, customer sync output, customer address
  sync output
- Infor Visual 9 product feed reads Infor Visual 9 product sync output; no template in this set
  writes Infor Visual 9 product sync output
- Infor Visual 9 part feed reads Infor Visual 9 product sync output, Infor Visual 9 part sync
  output; no template in this set writes Infor Visual 9 product sync output, Infor Visual 9 part
  sync output
- order feed reads account sync output, customer sync output, order sync output; no template in this
  set writes account sync output, customer sync output, order sync output
- order line feed reads order sync output, Infor Visual 9 product sync output, Infor Visual 9 part
  sync output, order line sync output; no template in this set writes order sync output, Infor
  Visual 9 product sync output, Infor Visual 9 part sync output, order line sync output
- receivable feed reads account sync output, customer sync output, receivable sync output; no
  template in this set writes account sync output, customer sync output, receivable sync output
- receivable line feed reads receivable sync output, order line sync output, receivable line sync
  output; no template in this set writes receivable sync output, order line sync output, receivable
  line sync output
- quote feed reads account sync output, customer sync output, quote sync output; no template in this
  set writes account sync output, customer sync output, quote sync output
- quote line feed reads quote sync output, Infor Visual 9 product sync output, Infor Visual 9 part
  sync output, quote line sync output; no template in this set writes quote sync output, Infor
  Visual 9 product sync output, Infor Visual 9 part sync output, quote line sync output
- quote price feed reads quote sync output, quote price sync output; no template in this set writes
  quote sync output, quote price sync output
- contact feed reads account sync output, contact sync output; no template in this set writes
  account sync output, contact sync output
- account note feed reads account sync output, account note sync output; no template in this set
  writes account sync output, account note sync output
- order note feed reads order sync output, order note sync output; no template in this set writes
  order sync output, order note sync output
- quote note feed reads quote sync output, quote note sync output; no template in this set writes
  quote sync output, quote note sync output
- Zoho quote feed reads Infor Visual 9 product sync output, account sync output, Zoho quote sync
  output; no template in this set writes Infor Visual 9 product sync output, account sync output,
  Zoho quote sync output
- Zoho sales order feed reads Infor Visual 9 product sync output, account sync output, Zoho sales
  order sync output; no template in this set writes Infor Visual 9 product sync output, account sync
  output, Zoho sales order sync output
- Zoho invoice feed reads Infor Visual 9 product sync output, account sync output, Zoho invoice sync
  output; no template in this set writes Infor Visual 9 product sync output, account sync output,
  Zoho invoice sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Zoho CRM pairs |
|---|---|---|---|
| Infor Visual Terms | Commercient Infor Visual terms object | 17 | record identifier → Commercient external key (custom field), record identifier → Name, Active flag → Active flag, Description → Description, Discount basis → Discount basis |
| Infor Visual Salesperson | Commercient Infor Visual salesperson object | 9 | record identifier → Commercient external key (custom field), Name → Name, Default commission percent → Default commission percentage, Earning code identifier → Earning code identifier, Employee identifier → Employee identifier |
| Account | Accounts | 15 | record identifier → Commercient AR customer code (Zoho field), Name → Account name, Sales rep identifier → Salesperson (custom field), Contact phone → Phone, Address line 1, Address line 2 → Billing street (Zoho field) |
| Infor Visual Customer | Commercient Infor Visual customer object | 135 | record identifier → Commercient external key (custom field), Name → Name, the linked Salesforce record → Account, Accept 830 → Accept 830 schedules, Accept 862 → Accept 862 schedules |
| Customer Reverse Lookup Account | Accounts | 2 | record identifier → Commercient AR customer code (Zoho field), the linked Salesforce record → Commercient Infor Visual customer (related record) |
| Infor Visual Customer Address | Commercient Infor Visual address object | 83 | returned customer identifier,Address number → Commercient external key (custom field), returned customer identifier,Address number → Name, the linked Salesforce record → Account, the linked Salesforce record → Customer, Active flag → Active flag |
| Product | Products | 7 | record identifier → Commercient external key (custom field), record identifier → Product name (Zoho field), ERP product code → ERP product code, Description → Description, Quantity on hand → Quantity in stock (Zoho field) |
| Infor Visual Part | Commercient Infor Visual part object | 89 | record identifier → Commercient external key (custom field), record identifier → Name, the linked Salesforce record → Product, Commercient ABC classification code → ABC classification code (custom field), Add forecast → Add forecast (custom field) |
| Infor Visual Order | Commercient Infor Visual order object | 114 | record identifier → Commercient external key (custom field), record identifier → Name, returned customer identifier → Account, returned customer identifier → Customer, Acceptance point → Accept early |
| Infor Visual Order Line | Commercient Infor Visual order line object | 99 | Customer order identifier, Line number → Commercient external key (custom field), Customer order identifier, Line number → Name, the linked Salesforce record → Infor Visual order lookup (custom field), Part identifier → Part (custom field), Part identifier → Product (custom field) |
| Infor Visual Receivable | Commercient Infor Visual receivable object | 50 | Invoice identifier → Commercient external key (custom field), Invoice identifier → Name, returned customer identifier → Account, returned customer identifier → Customer, Buy rate → Buy rate |
| Infor Visual Receivable Line | Commercient Infor Visual receivable line object | 31 | Invoice identifier, Line number → Commercient external key (custom field), Invoice identifier, Line number → Name, the linked Salesforce record → Receivable, the linked Salesforce record → Order line number, Amount → Amount |
| Infor Visual Quote | Commercient Infor Visual quote object | 66 | record identifier → Commercient external key (custom field), record identifier → Name, returned customer identifier → Account, returned customer identifier → Customer, Address line 1 → Address line 1 |
| Infor Visual Quote Line | Commercient Infor Visual quote line object | 46 | Quote identifier, Line number → Commercient external key (custom field), Quote identifier, Line number → Name, Quote identifier → Quote, Part identifier → Part, Part identifier → Product |
| Infor Visual Quote Price | Commercient Infor Visual Quote Price (Zoho package object) | 30 | Quote identifier, Quote line number, Quantity → Commercient external key (custom field), Quote identifier, Quote line number, Quantity → Name, the linked Salesforce record → Quote, Burden general selling and administrative → Burden general selling and administrative, Burden markup → Burden markup |
| Contact | Contacts | 9 | returned customer identifier, Contact number → Commercient external key column, the linked Salesforce record → Account name, Contact last name → Last name, Contact first name → First name, Contact email → Email |
| Account Notes | Infor Visual notes (custom object) | 5 | Row identifier → Name, Note → Notes, the linked Salesforce record → Account, → Row timestamp, Row identifier → Commercient external key column |
| Order Notes | Infor Visual notes (custom object) | 5 | Row identifier → Name, Note → Notes, the linked Salesforce record → Order, → Row timestamp, Row identifier → Commercient external key column |
| Quote Notes | Infor Visual notes (custom object) | 5 | Row identifier → Name, Note → Notes, the linked Salesforce record → Quotes, → Row timestamp, Row identifier → Commercient external key column |
| Quotes | Quotes | 18 | record identifier → Commercient external key (custom field), returned customer identifier → Account name, record identifier → Name, record identifier → returned quote number, Create date → ERP created date (custom field) |
| Sales orders | Sales orders | 17 | record identifier → Commercient external key (custom field), returned customer identifier → Account name, record identifier → Name, record identifier → Sales order number (Zoho field), Create date → ERP created date (custom field) |
| Invoices | Invoices | 17 | Invoice identifier → Commercient external key (custom field), Invoice identifier → Name, Invoice identifier → Invoice number (Zoho field), Create date → Invoice date, Invoice identifier → Subject |

## 6. Community templates

The catalogue carries 23 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 23
- Default operations: insert on 23, update on 23, delete on 23
- Marked as circular sync: 0
- Licence groups they span: 5
- Destination objects: Infor Visual notes (custom object), Accounts, Commercient Infor Visual
  address object, Commercient Infor Visual customer object, Commercient Infor Visual order object,
  Commercient Infor Visual order line object, Commercient Infor Visual part object, Commercient
  Infor Visual quote line object, Commercient Infor Visual Quote Price (Zoho package object),
  Commercient Infor Visual quote object, Commercient Infor Visual receivable object, Commercient
  Infor Visual salesperson object, Commercient Infor Visual terms object, Contacts, Invoices,
  Products, Quotes, Sales orders, users and a custom object
- Object display names: Account, Account Notes, Contact, Customer Reverse Lookup Account, Infor
  Visual Customer, Infor Visual Customer Address, Infor Visual Order, Infor Visual Order Line, Infor
  Visual Part, Infor Visual Quote, Infor Visual Quote Line, Infor Visual Quote Price and 11 more
- Template groups: Account, CRM Order and Line, CRM Quote and Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/infor-visual-9`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Infor Visual 9 → Zoho CRM templates set up. dlake-crmpro-zohocrm is the destination
skill this page sits under: its own text is the authority for the Zoho CRM conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-zohocrm/erps/infor-visual-9`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
