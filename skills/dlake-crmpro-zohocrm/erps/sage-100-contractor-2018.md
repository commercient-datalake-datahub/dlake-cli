---
name: dlake-crmpro-zohocrm/erps/sage-100-contractor-2018
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 100 Contractor 2018 → Zoho CRM template set, when
  deciding which templates to import and activate, or when a run completes without pushing records
  and the answer is in the view or the configuration row. It extends dlake-crmpro, which covers
  operating CRMPro generally, and dlake-crmpro-zohocrm, the destination skill this page is a child
  of, which carries the Zoho CRM conventions that hold across every ERP.
---
# CRMPro → Zoho CRM — Sage 100 Contractor 2018: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-100-contractor-2018` (or `list_skills`)
against the Commercient admin plane. Existing customers who need access or help: contact
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
| **Sage Sales Person** | The templates push Commercient Sage 100 Contractor Salesperson object to Zoho CRM. | Commercient Sage 100 Contractor Salesperson object | employees |
| **Accounts** | The templates push Accounts to Zoho CRM. | Accounts | clients |
| **Sage Customer** | The templates push Commercient Sage 100 Contractor Customer object to Zoho CRM. | Commercient Sage 100 Contractor Customer object | clients |
| **Sage Job** | The templates push Commercient Sage 100 Contractor Job object to Zoho CRM. | Commercient Sage 100 Contractor Job object | jobs |
| **Products** | The templates push Products to Zoho CRM. | Products | parts |
| **Contacts** | The templates push Contacts to Zoho CRM. | Contacts | client contacts |
| **Sage Parts** | The templates push Commercient Sage 100 Contractor Parts object to Zoho CRM. | Commercient Sage 100 Contractor Parts object | parts |
| **Sage Inventory History** | The templates push Commercient Sage 100 Contractor Inventory History object to Zoho CRM. | Commercient Sage 100 Contractor Inventory History object | inventory history |
| **Sage AR Invoice** | The templates push Commercient Sage 100 Contractor AR Invoice object to Zoho CRM. | Commercient Sage 100 Contractor AR Invoice object | AR invoices, jobs |
| **Sage AR invoice line** | The templates push Commercient Sage 100 Contractor AR Invoice Line object to Zoho CRM. | Commercient Sage 100 Contractor AR Invoice Line object | AR invoice lines |
| **Sage AR Payments** | The templates push Commercient Sage 100 Contractor AR Payment object to Zoho CRM. | Commercient Sage 100 Contractor AR Payment object | AR payments |
| **Quote** | The templates push Quotes to Zoho CRM. | Quotes | service invoice lines, parts, service invoices, jobs, clients, x |
| **Sales order** | The templates push Sales orders to Zoho CRM. | Sales orders | service invoice lines, parts, service invoices, jobs, clients, x |
| **Invoice** | The templates push Invoices to Zoho CRM. | Invoices | service invoice lines, parts, service invoices, jobs, clients, x |
| **CRM Ownership** | The templates push users to Zoho CRM. | users | — |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Users | users | Commercient external key column | 1 |
| Sage Sales Person | Commercient Sage 100 Contractor Salesperson object | Commercient external key column | 2 |
| Accounts | Accounts | Commercient AR customer code (Zoho field) | 3 |
| Sage Customer | Commercient Sage 100 Contractor Customer object | Commercient external key column | 4 |
| Sage Job | Commercient Sage 100 Contractor Job object | Commercient external key column | 5 |
| Products | Products | Commercient external key column | 6 |
| Contacts | Contacts | Commercient external key column | 7 |
| Sage Parts | Commercient Sage 100 Contractor Parts object | Commercient external key column | 8 |
| Sage Inventory History | Commercient Sage 100 Contractor Inventory History object | Commercient external key column | 9 |
| Sage AR Invoice | Commercient Sage 100 Contractor AR Invoice object | Commercient external key column | 10 |
| Sage AR invoice line | Commercient Sage 100 Contractor AR Invoice Line object | Commercient external key column | 11 |
| Sage AR Payments | Commercient Sage 100 Contractor AR Payment object | Commercient external key column | 12 |
| Quote | Quotes | Commercient external key column | 13 |
| Sales order | Sales orders | Commercient external key column | 14 |
| Invoice | Invoices | Commercient external key column | 15 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert only | employees |
| account feed | insert only | clients |
| customer feed | insert only | clients |
| job feed | insert only | jobs |
| product feed | insert only | parts |
| contact feed | insert + update | client contacts |
| parts feed | insert only | parts |
| inventory history feed | insert only | inventory history |
| AR invoice feed | insert only | AR invoices, jobs |
| AR invoice line feed | insert only | AR invoice lines |
| AR payment feed | insert only | AR payments |
| quote feed | insert only | service invoice lines, parts, service invoices, jobs, clients |
| sales order feed | insert only | service invoice lines, parts, service invoices, jobs, clients |
| invoice feed | insert only | service invoice lines, parts, service invoices, jobs, clients |

## 4. Order of work

The templates set run sequence from 1 to 15. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Users
- 2 — Sage Sales Person
- 3 — Accounts
- 4 — Sage Customer
- 5 — Sage Job
- 6 — Products
- 7 — Contacts
- 8 — Sage Parts
- 9 — Sage Inventory History
- 10 — Sage AR Invoice
- 11 — Sage AR invoice line
- 12 — Sage AR Payments
- 13 — Quote
- 14 — Sales order
- 15 — Invoice

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- salesperson feed reads salesperson sync output; no template in this set writes salesperson sync
  output
- account feed reads salesperson sync output, account sync output, user sync output (generic name);
  no template in this set writes salesperson sync output, account sync output, user sync output
  (generic name)
- customer feed reads account sync output, salesperson sync output, customer sync output; no
  template in this set writes account sync output, salesperson sync output, customer sync output
- job feed reads account sync output, customer sync output, salesperson sync output, job sync
  output; no template in this set writes account sync output, customer sync output, salesperson sync
  output, job sync output
- product feed reads product sync output; no template in this set writes product sync output
- contact feed reads account sync output, contact sync output; no template in this set writes
  account sync output, contact sync output
- parts feed reads product sync output, parts sync output; no template in this set writes product
  sync output, parts sync output
- inventory history feed reads product sync output, parts sync output, inventory history sync
  output; no template in this set writes product sync output, parts sync output, inventory history
  sync output
- AR invoice feed reads account sync output, customer sync output, job sync output, AR invoice sync
  output; no template in this set writes account sync output, customer sync output, job sync output,
  AR invoice sync output
- AR invoice line feed reads AR invoice sync output, product sync output, parts sync output, AR
  invoice line sync output; no template in this set writes AR invoice sync output, product sync
  output, parts sync output, AR invoice line sync output
- AR payment feed reads AR invoice sync output, AR payment sync output; no template in this set
  writes AR invoice sync output, AR payment sync output
- quote feed reads product sync output, account sync output, salesperson sync output, quote sync
  output; no template in this set writes product sync output, account sync output, salesperson sync
  output, quote sync output
- sales order feed reads product sync output, account sync output, salesperson sync output, quote
  sync output, sales order sync output; no template in this set writes product sync output, account
  sync output, salesperson sync output, quote sync output
- invoice feed reads product sync output, account sync output, salesperson sync output, sales order
  sync output, invoice sync output; no template in this set writes product sync output, account sync
  output, salesperson sync output, sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Zoho CRM pairs |
|---|---|---|---|
| Users | users | 1 | Commercient external key column → Commercient external key column |
| Sage Sales Person | Commercient Sage 100 Contractor Salesperson object | 79 | Record number → Commercient external key (custom field), Employee first name, Employee last name → Name, Aatrix electronic Affordable Care Act form consent → Aatrix electronic Affordable Care Act form consent (custom field), Aatrix electronic wage statement consent → Aatrix electronic wage statement consent (custom field), Electronic funds transfer status → Account status (custom field) |
| Accounts | Accounts | 16 | Record number → Commercient AR customer code (Zoho field), Client name → Account name, Street address line 1, Street address line 2 → Billing street (Zoho field), City name → Billing city (Zoho field), State → Billing state |
| Sage Customer | Commercient Sage 100 Contractor Customer object | 108 | Record number → Commercient external key (custom field), Client name → Name, Street address line 1 → Address 1, Street address line 2 → Address 2, Region → Area |
| Sage Job | Commercient Sage 100 Contractor Job object | 76 | Record number → Commercient external key (custom field), Job name → Name, Client number → Accounts, Client number → Sage 100 Contractor customer (related record), Sales employee → Sage 100 Contractor sales rep (related record) |
| Products | Products | 7 | Record number → Commercient external key column, Part name → Product name (Zoho field), Record number → ERP product code, Note text → Description, Inactive → Product active (Zoho field) |
| Contacts | Contacts | 9 | Record number,Line number → Commercient external key column, Contact name → Last name, Contact name → First name, Email → Email, Phone number → Home phone |
| Sage Parts | Commercient Sage 100 Contractor Parts object | 38 | Record number → Commercient external key (custom field), Part name → Name, Alpha part number → Alpha part number (Zoho field), Average cost → Average cost (Zoho field), Part billing amount → Billing amount (Zoho field) |
| Sage Inventory History | Commercient Sage 100 Contractor Inventory History object | 20 | Record number → Commercient external key (custom field), Record number → Name, Entry date → Entry date (custom field), Ledger record → Ledger reference number (custom field), Location number → Location (custom field) |
| Sage AR Invoice | Commercient Sage 100 Contractor AR Invoice object | 51 | Record number → Commercient external key (custom field), Record number, Invoice number → Custom module name (custom field), Record number, Invoice number → Name, Invoice balance → Balance, Purchase order → Client PO (Zoho field) |
| Sage AR invoice line | Commercient Sage 100 Contractor AR Invoice Line object | 27 | Record number, Line number → Commercient external key (custom field), Record number, Line number → Name, Ledger account → Account, Alpha part number → Alpha part number (custom field), Cost code → Cost code (custom field) |
| Sage AR Payments | Commercient Sage 100 Contractor AR Payment object | 13 | Amount → Amount paid, Applied credit → Credit taken (Zoho field), Check date → Date, Payment description → Description, Discount taken → Discount taken (Zoho field) |
| Quote | Quotes | 16 | Record number → Commercient external key (custom field), the linked Salesforce record → Account name, Record number → Name, Record number → returned quote number, Inserted date → ERP created date (custom field) |
| Sales order | Sales orders | 17 | Record number → Commercient external key (custom field), the linked Salesforce record → Account name, the linked Salesforce record → quote name lookup, Record number → Name, Record number → Sales order number (Zoho field) |
| Invoice | Invoices | 16 | Record number → Commercient external key (custom field), the linked Salesforce record → Account name, the linked Salesforce record → Sales order (single record), Record number → Name, Record number → Invoice number (Zoho field) |

## 6. Community templates

The catalogue carries 14 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 14
- Default operations: insert on 14, update on 14, delete on 14
- Marked as circular sync: 0
- Licence groups they span: 5
- Destination objects: Accounts, Contacts, Invoices, Products, Quotes, Commercient Sage 100
  Contractor AR Payment object, Commercient Sage 100 Contractor AR Invoice object, Commercient Sage
  100 Contractor AR Invoice Line object, Commercient Sage 100 Contractor Customer object,
  Commercient Sage 100 Contractor Inventory History object, Commercient Sage 100 Contractor Job
  object, Commercient Sage 100 Contractor Parts object, Commercient Sage 100 Contractor Salesperson
  object, Sales orders
- Object display names: Accounts, Contacts, Invoice, Products, Quote, Sage AR Invoice, Sage AR
  invoice line, Sage AR Payments, Sage Customer, Sage Inventory History, Sage Job, Sage Parts and 2
  more
- Template groups: Account, CRM Order and Line, CRM Quote and Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-100-contractor-2018`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 100 Contractor 2018 → Zoho CRM templates set up. dlake-crmpro-zohocrm is the
destination skill this page sits under: its own text is the authority for the Zoho CRM conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-100-contractor-2018`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
