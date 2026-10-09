---
name: dlake-crmpro-zohocrm/erps/exact-online
kind: erp-summary
description: >-
  Use it when standing up or reading an Exact Online → Zoho CRM template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-zohocrm, the destination skill this page is a child of, which carries
  the Zoho CRM conventions that hold across every ERP.
---
# CRMPro → Zoho CRM — Exact Online: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/exact-online` (or `list_skills`) against the
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
| **Accounts** | The templates push Accounts to Zoho CRM. | Accounts | accounts |
| **Exact Online Customer** | The templates push Commercient Exact Online customer object to Zoho CRM. | Commercient Exact Online customer object | accounts |
| **Exact Online Address** | The templates push Commercient Exact Online address object to Zoho CRM. | Commercient Exact Online address object | addresses |
| **Contacts** | The templates push Contacts to Zoho CRM. | Contacts | contacts |
| **Products** | The templates push Products to Zoho CRM. | Products | items |
| **Exact Online Sales order** | The templates push Commercient Exact Online sales order object to Zoho CRM. | Commercient Exact Online sales order object | sales orders |
| **Exact Online Sales order line** | The templates push Commercient Exact Online sales order line object to Zoho CRM. | Commercient Exact Online sales order line object | sales order lines |
| **Exact Online Invoice** | The templates push Commercient Exact Online invoice object to Zoho CRM. | Commercient Exact Online invoice object | invoices |
| **Exact Online invoice line** | The templates push Commercient Exact Online invoice line object to Zoho CRM. | Commercient Exact Online invoice line object | invoice lines, invoices |
| **Exact Online Quotes** | The templates push Commercient Exact Online quote object to Zoho CRM. | Commercient Exact Online quote object | quotes |
| **Exact Online Quote line number** | The templates push Commercient Exact Online quote line object to Zoho CRM. | Commercient Exact Online quote line object | quote lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Accounts | Accounts | Commercient AR customer code (Zoho field) | 1 |
| Exact Online Customer | Commercient Exact Online customer object | Commercient external key column | 2 |
| Exact Online Address | Commercient Exact Online address object | Commercient external key column | 3 |
| Contacts | Contacts | Commercient external key column | 4 |
| Products | Products | Commercient external key column | 5 |
| Exact Online Sales order | Commercient Exact Online sales order object | Commercient external key column | 6 |
| Exact Online Sales order line | Commercient Exact Online sales order line object | Commercient external key column | 7 |
| Exact Online Invoice | Commercient Exact Online invoice object | Commercient external key column | 8 |
| Exact Online invoice line | Commercient Exact Online invoice line object | Commercient external key column | 9 |
| Exact Online Quotes | Commercient Exact Online quote object | Commercient external key column | 10 |
| Exact Online Quote line number | Commercient Exact Online quote line object | Commercient external key column | 11 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert only | accounts |
| customer feed | insert + update | accounts |
| address feed | insert + update | addresses |
| contact feed | insert only | contacts |
| product feed | insert only | items |
| sales order feed | insert + update | sales orders |
| sales order line feed | insert + update | sales order lines |
| invoice feed | insert + update | invoices |
| invoice line feed | insert + update | invoice lines, invoices |
| quote feed | insert only | quotes |
| quote line feed | insert only | quote lines |

## 4. Order of work

The templates set run sequence from 1 to 11. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Accounts
- 2 — Exact Online Customer
- 3 — Exact Online Address
- 4 — Contacts
- 5 — Products
- 6 — Exact Online Sales order
- 7 — Exact Online Sales order line
- 8 — Exact Online Invoice
- 9 — Exact Online invoice line
- 10 — Exact Online Quotes
- 11 — Exact Online Quote line number

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads account sync output (generic name); no template in this set writes account sync
  output (generic name)
- customer feed reads account sync output (generic name), customer sync output (generic name); no
  template in this set writes account sync output (generic name), customer sync output (generic
  name)
- address feed reads account sync output (generic name), customer sync output (generic name),
  address sync output (generic name); no template in this set writes account sync output (generic
  name), customer sync output (generic name), address sync output (generic name)
- contact feed reads account sync output (generic name), contact sync output (generic name); no
  template in this set writes account sync output (generic name), contact sync output (generic name)
- product feed reads product sync output (generic name); no template in this set writes product sync
  output (generic name)
- sales order feed reads account sync output (generic name), customer sync output (generic name),
  sales order sync output (generic name); no template in this set writes account sync output
  (generic name), customer sync output (generic name), sales order sync output (generic name)
- sales order line feed reads sales order sync output (generic name), sales order line sync output
  (generic name); no template in this set writes sales order sync output (generic name), sales order
  line sync output (generic name)
- invoice feed reads account sync output (generic name), customer sync output (generic name), sales
  order sync output (generic name), invoice sync output (generic name); no template in this set
  writes account sync output (generic name), customer sync output (generic name), sales order sync
  output (generic name), invoice sync output (generic name)
- invoice line feed reads invoice sync output (generic name), invoice line sync output (generic
  name); no template in this set writes invoice sync output (generic name), invoice line sync output
  (generic name)
- quote feed reads account sync output (generic name), customer sync output (generic name), quote
  sync output (generic name); no template in this set writes account sync output (generic name),
  customer sync output (generic name), quote sync output (generic name)
- quote line feed reads quote sync output (generic name), quote line sync output (generic name); no
  template in this set writes quote sync output (generic name), quote line sync output (generic
  name)

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Zoho CRM pairs |
|---|---|---|---|
| Accounts | Accounts | 12 | record identifier → Commercient external key (custom field), record identifier → Commercient AR customer code (Zoho field), Name → Account name, Address line 1, Address line 2, Address line 3 → Billing street (Zoho field), City → Billing city (Zoho field) |
| Exact Online Customer | Commercient Exact Online customer object | 150 | record identifier → Commercient external key (custom field), Name → Name, Accountant → Accountant, Account manager → Commercient account manager, Account manager full name → Commercient account manager full name |
| Exact Online Address | Commercient Exact Online address object | 57 | record identifier → Commercient external key (custom field), record identifier → Name, Account → Account, Account is supplier → Commercient account is supplier, Account name (ERP column) → Account name |
| Contacts | Contacts | 18 | Account → Account name, Birth date → Date of birth (Zoho field), Business fax → Fax, Given name → First name, Start date → Date met (Zoho field) |
| Products | Products | 7 | Description → Product name (Zoho field), Code → ERP product code, record identifier → Commercient external key (custom field), Description → Description, Standard cost price → Unit price |
| Exact Online Sales order | Commercient Exact Online sales order object | 62 | Order number → Commercient external key (custom field), Order number → Name, Amount in default currency → Commercient amount in default currency, Discount amount excluding VAT → Commercient discount amount excluding VAT, Amount in foreign currency excluding VAT → Commercient amount in foreign currency excluding VAT |
| Exact Online Sales order line | Commercient Exact Online sales order line object | 46 | record identifier → Commercient external key (custom field), Order number, Line number → Name, Amount in default currency → Commercient amount in default currency, Amount in foreign currency → Commercient amount in foreign currency, Cost center → Commercient cost center |
| Exact Online Invoice | Commercient Exact Online invoice object | 71 | returned invoice identifier → Commercient external key (custom field), returned invoice number → Name, Amount in foreign currency → Commercient amount in foreign currency, Discount amount → Commercient discount amount, Amount in default currency → Commercient amount in default currency |
| Exact Online invoice line | Commercient Exact Online invoice line object | 52 | record identifier → Commercient external key (custom field), returned invoice number, Line number → Name, Amount in default currency → Commercient amount in default currency, Amount in foreign currency → Commercient amount in foreign currency, Cost center → Commercient cost center |
| Exact Online Quotes | Commercient Exact Online quote object | 52 | Quotation identifier → Commercient external key (custom field), Order account → Account, Order account → Commercient Exact Online customer (related record), Quotation number → Name, Amount in default currency → Commercient amount in default currency |
| Exact Online Quote line number | Commercient Exact Online quote line object | 24 | record identifier → Commercient external key (custom field), Quotation identifier → Exact Online quote lookup (custom field), Quotation number, Line number → Name, Amount in default currency → Commercient amount in default currency, Amount in foreign currency → Commercient amount in foreign currency |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/exact-online`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Exact Online → Zoho CRM templates set up. dlake-crmpro-zohocrm is the destination skill
this page sits under: its own text is the authority for the Zoho CRM conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-zohocrm/erps/exact-online`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
