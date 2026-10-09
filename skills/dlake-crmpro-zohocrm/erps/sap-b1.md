---
name: dlake-crmpro-zohocrm/erps/sap-b1
kind: erp-summary
description: >-
  Use it when standing up or reading a SAP Business One → Zoho CRM template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-zohocrm, the destination skill this page is a child of, which carries
  the Zoho CRM conventions that hold across every ERP.
---
# CRMPro → Zoho CRM — SAP Business One: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/sap-b1` (or `list_skills`) against the Commercient
admin plane. Existing customers who need access or help: contact support@commercient.com. New
customers: contact sales@commercient.com to become a customer and be whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-zohocrm is the
destination skill this page is a child of, and the authority for the Zoho CRM conventions that hold
across every ERP: read it first, then come back here for what this source's own templates set. This
page grows as the catalogue does.

## 1. What the templates deliver

| Group | Business outcome | Objects | Source tables and views |
|---|---|---|---|
| **Accounts** | The templates push Accounts to Zoho CRM. | Accounts | business partners, business partner addresses, business partner groups |
| **Customer** | The templates push Commercient SAP Business One Customer object to Zoho CRM. | Commercient SAP Business One Customer object | business partners |
| **Address** | The templates push Commercient SAP Business One Address object to Zoho CRM. | Commercient SAP Business One Address object | business partner addresses |
| **Products** | The templates push Products to Zoho CRM. | Products | items, item prices |
| **Item Master** | The templates push Commercient SAP Business One Item object to Zoho CRM. | Commercient SAP Business One Item object | items |
| **Sales Order** | The templates push Commercient SAP Business One Sales Order Header object to Zoho CRM. | Commercient SAP Business One Sales Order Header object | sales orders |
| **Sales Order Line** | The templates push Commercient SAP Business One Sales Order Detail object to Zoho CRM. | Commercient SAP Business One Sales Order Detail object | sales order lines, R, sales orders |
| **Invoice** | The templates push Commercient SAP Business One Invoice Header object to Zoho CRM. | Commercient SAP Business One Invoice Header object | AR invoice documents |
| **Invoice Detail** | The templates push Commercient SAP Business One Invoice Detail object to Zoho CRM. | Commercient SAP Business One Invoice Detail object | invoice lines, a, T, AR invoice documents |
| **Price Books** | The templates push Commercient SAP Business One Price Books object to Zoho CRM. | Commercient SAP Business One Price Books object | price lists |
| **Item Price Books** | The templates push Commercient SAP Business One Item Price Books object to Zoho CRM. | Commercient SAP Business One Item Price Books object | item prices |
| **Price Book** | The templates push Zoho price books to Zoho CRM. | Zoho price books | price lists |
| **SAP Business One Product Price Books** | The templates push Zoho product price book relation to Zoho CRM. | Zoho product price book relation | item prices, price lists |
| **Contacts** | The templates push Contacts to Zoho CRM. | Contacts | business partner contacts |
| **SAP Business One Quotes** | The templates push Commercient SAP Business One Quotes object to Zoho CRM. | Commercient SAP Business One Quotes object | sales quotations |
| **SAP Business One Quotes Details** | The templates push Commercient SAP Business One Quotes Details object to Zoho CRM. | Commercient SAP Business One Quotes Details object | quotation lines, sales orders |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Accounts | Accounts | Business partner code (custom field) | 1 |
| Customer | Commercient SAP Business One Customer object | Commercient external key column | 2 |
| Address | Commercient SAP Business One Address object | Commercient external key column | 3 |
| Products | Products | Commercient external key column | 4 |
| Item Master | Commercient SAP Business One Item object | Commercient external key column | 5 |
| Sales Order | Commercient SAP Business One Sales Order Header object | Commercient external key column | 6 |
| Sales Order Line | Commercient SAP Business One Sales Order Detail object | Commercient external key column | 7 |
| Invoice | Commercient SAP Business One Invoice Header object | Commercient external key column | 8 |
| Invoice Detail | Commercient SAP Business One Invoice Detail object | Commercient external key column | 9 |
| Price Books | Commercient SAP Business One Price Books object | Commercient external key column | 10 |
| Item Price Books | Commercient SAP Business One Item Price Books object | Commercient external key column | 11 |
| Price Book | Zoho price books | Commercient external key column | 12 |
| SAP Business One Product Price Books | Zoho product price book relation | Commercient external key column | 13 |
| Contacts | Contacts | Commercient external key column | 14 |
| SAP Business One Quotes | Commercient SAP Business One Quotes object | Commercient external key column | 15 |
| SAP Business One Quotes Details | Commercient SAP Business One Quotes Details object | Commercient external key column | 16 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert only | business partners, business partner addresses, business partner groups |
| customer feed | insert only | business partners |
| address feed | insert only | business partner addresses |
| product feed | insert only | items, item prices |
| item feed | insert only | items |
| sales order feed | insert only | sales orders |
| sales order line feed | insert only | sales order lines, R, sales orders |
| invoice feed | insert only | AR invoice documents |
| invoice line feed | insert only | invoice lines, a, T, AR invoice documents |
| price book feed | insert only | price lists |
| item price book feed | insert only | item prices |
| price book feed (standard module) | insert only | price lists |
| product price list feed | insert + update | item prices, price lists |
| contact list feed | insert only | business partner contacts |
| quote feed | insert only | sales quotations |
| quote line feed | insert only | quotation lines, sales orders |

## 4. Order of work

The templates set run sequence from 1 to 16. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Accounts
- 2 — Customer
- 3 — Address
- 4 — Products
- 5 — Item Master
- 6 — Sales Order
- 7 — Sales Order Line
- 8 — Invoice
- 9 — Invoice Detail
- 10 — Price Books
- 11 — Item Price Books
- 12 — Price Book
- 13 — SAP Business One Product Price Books
- 14 — Contacts
- 15 — SAP Business One Quotes
- 16 — SAP Business One Quotes Details

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- customer feed reads account: customer sync output; no template in this set writes account:
  customer sync output
- address feed reads account: customer sync output, address sync output; no template in this set
  writes account: customer sync output, address sync output
- item feed reads Products: item master sync output; no template in this set writes Products: item
  master sync output
- sales order feed reads account: customer sync output, sales order sync output; no template in this
  set writes account: customer sync output, sales order sync output
- sales order line feed reads sales order sync output, item master sync output, sales order line
  sync output; no template in this set writes sales order sync output, item master sync output,
  sales order line sync output
- invoice feed reads account: customer sync output, invoice sync output; no template in this set
  writes account: customer sync output, invoice sync output
- invoice line feed reads invoice line sync output, invoice sync output, item master sync output; no
  template in this set writes invoice line sync output, invoice sync output, item master sync output
- price book feed reads price book sync output; no template in this set writes price book sync
  output
- item price book feed reads item master sync output, price book sync output, item price book sync
  output; no template in this set writes item master sync output, price book sync output, item price
  book sync output
- price book feed (standard module) reads Price Books; no template in this set writes Price Books
- product price list feed reads Price Books, Products: product price list sync output; no template
  in this set writes Price Books, Products: product price list sync output
- contact list feed reads account: contact sync output; no template in this set writes account:
  contact sync output
- quote feed reads account: customer sync output, quote sync output; no template in this set writes
  account: customer sync output, quote sync output
- quote line feed reads quote sync output, item master sync output, quote line sync output; no
  template in this set writes quote sync output, item master sync output, quote line sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Zoho CRM pairs |
|---|---|---|---|
| Accounts | Accounts | 16 | Business partner code → Commercient external key (custom field), Business partner code → Commercient AR customer code (SAP Business One package field), Business partner code → Business partner code (custom field), Business partner name → Account name, Business partner territory → Territory |
| Customer | Commercient SAP Business One Customer object | 125 | the linked Salesforce record → Account, Business partner code → Commercient external key (custom field), Business partner code → external key column, Business partner code → Name, Additional identifier → Additional identifier (custom field) |
| Address | Commercient SAP Business One Address object | 33 | Address,Business partner code,Address type → Commercient external key (custom field), Address,Business partner code,Address type → external key column, Address → Address, Address,Business partner code,Address type → Name, Address type code → Address type field |
| Products | Products | 8 | Item code → Commercient external key column, Item name → Product name (Zoho field), Item code → ERP product code, Price per unit → Unit price, Product source → Product category |
| Sales Order | Commercient SAP Business One Sales Order Header object | 97 | returned document entry → Commercient external key (custom field), returned document entry → Name, Address → Address, Address 2 → Address 2, Attachment entry → Attachment entry (custom field) |
| Sales Order Line | Commercient SAP Business One Sales Order Detail object | 117 | returned document entry,ERP line number → Commercient external key (custom field), general ledger account code → Account code (custom field), Address → Address, Back order allowed → Back order (Zoho field), Base business partner code → Base card (custom field) |
| Invoice | Commercient SAP Business One Invoice Header object | 111 | returned document entry → Commercient external key (custom field), returned document entry → Name, Address → Address, Address 2 → Address 2, Agent code → Agent code (custom field) |
| Invoice Detail | Commercient SAP Business One Invoice Detail object | 119 | Invoice line key → Commercient external key (custom field), general ledger account code → Account code (custom field), Address → Address (custom field), Back order allowed → Back order (custom field), Base business partner code → Base card (custom field) |
| Price Books | Commercient SAP Business One Price Books object | 28 | Price list number → Commercient external key (custom field), Price list number → external key column, Additional currency 1 → Additional currency 1 (custom field), Additional currency 2 → Additional currency 2 (custom field), Base price list number → Base number (custom field) |
| Item Price Books | Commercient SAP Business One Item Price Books object | 21 | Additional price 1 → Additional price 1 (custom field), Additional price 2 → Additional price 2 (custom field), Item base price list number → Base price list number (custom field), Currency → Currency name (custom field), Additional price 1 currency → Additional price 1 currency |
| Price Book | Zoho price books | 5 | Price list number → Commercient external key (custom field), Price list number → external key column, Price list name → Price Book Name, Price list name → Price book name (Zoho field), Price list active → Active |
| SAP Business One Product Price Books | Zoho product price book relation | 5 | Item code,Price list → Commercient external key column, Price list name → Price book name, Price → List price, Price list → Price book lookup (Zoho field), Item code → Product lookup (Zoho field) |
| Contacts | Contacts | 10 | Contact code → Commercient external key column, Business partner code → Account name, Position → Role, Name → Last name, Name → First name |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/sap-b1`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped SAP Business One → Zoho CRM templates set up. dlake-crmpro-zohocrm is the destination
skill this page sits under: its own text is the authority for the Zoho CRM conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-zohocrm/erps/sap-b1`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
