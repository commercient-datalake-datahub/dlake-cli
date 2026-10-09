---
name: dlake-crmpro-salesforce/erps/quickbooks-desktop
kind: erp-summary
description: >-
  Use it when standing up or reading a QuickBooks Desktop → Salesforce template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a child of,
  which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — QuickBooks Desktop: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/quickbooks-desktop` (or `list_skills`) against
the Commercient admin plane. Existing customers who need access or help: contact
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
| **GET USER** | The templates push users to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | users | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created and existing ones updated; none are deleted. | account, Commercient Terms Managed Custom Object, Contact, Commercient Customer Managed Custom Object, Commercient Sales Rep Managed Custom Object | customers, terms, contacts, sales reps |
| **CRM Opportunity and Line** | ERP estimate, Estimate line data becomes Opportunity, Opportunity line item in Salesforce. New records are created and existing ones updated; none are deleted. | Opportunity, Opportunity line item | estimates, order entry headers, estimate lines, inventory items |
| **CRM Quote and Line** | ERP estimate, Estimate line data becomes Quote, Quote line item in Salesforce. New records are created and existing ones updated; none are deleted. | Quote, Quote line item | estimates, estimate lines, inventory items |
| **Product** | ERP Item inventory data becomes Commercient QuickBooks Item Inventory Managed Custom Object, Product in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient QuickBooks Item Inventory Managed Custom Object, Product | inventory items |
| **Open AR Invoice Header** | The detail lines on open invoices are visible too so that you are aware of what items you are awaiting payment on from your customer. New records are created and existing ones updated; none are deleted. | Commercient Invoice Line Managed Custom Object, Commercient Invoice Managed Custom Object | invoice lines, invoices |
| **Sales order** | The templates push Commercient Sales Order Line Managed Custom Object, Commercient Sales Order Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Sales Order Line Managed Custom Object, Commercient Sales Order Managed Custom Object | sales order lines, sales orders |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| Terms | Commercient Terms Managed Custom Object | Commercient external key (QuickBooks package) | 1 |
| Sales rep | Commercient Sales Rep Managed Custom Object | Commercient external key (QuickBooks package) | 2 |
| CRM Account | account | Commercient AR customer code | 3 |
| Customer | Commercient Customer Managed Custom Object | Commercient external key (QuickBooks package) | 4 |
| Sales order | Commercient Sales Order Managed Custom Object | Commercient external key (QuickBooks package) | 5 |
| Sales order line | Commercient Sales Order Line Managed Custom Object | Commercient external key (QuickBooks package) | 6 |
| Invoice | Commercient Invoice Managed Custom Object | Commercient external key (QuickBooks package) | 7 |
| Invoice line | Commercient Invoice Line Managed Custom Object | Commercient external key (QuickBooks package) | 8 |
| Item inventory | Commercient QuickBooks Item Inventory Managed Custom Object | Commercient external key (QuickBooks package) | 10 |
| Contact | Contact | External key (custom field) | 15 |
| Product | Product | Commercient external key (QuickBooks package) | 16 |
| Opportunity | Opportunity | External key (custom field) | 17 |
| Opportunity line item | Opportunity line item | External key (custom field) | 18 |
| Quote | Quote | External key (custom field) | 19 |
| Quote line item | Quote line item | External key (custom field) | 20 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| terms feed | insert + update | terms |
| sales rep feed | insert + update | sales reps |
| CRM account feed | insert + update | customers |
| customer feed | insert + update | customers |
| sales order feed | insert + update | sales orders |
| sales order line feed | insert + update | sales order lines |
| invoice feed | insert + update | invoices |
| invoice line feed | insert + update | invoice lines |
| item inventory feed | insert + update | inventory items |
| contacts feed | insert + update | contacts |
| product feed | insert + update | inventory items |
| opportunity feed | insert only | estimates, order entry headers |
| opportunity line item feed | insert only | estimate lines, estimates, inventory items |
| quote feed | insert only | estimates |
| quote line item feed | insert only | estimate lines, estimates, inventory items |

## 4. Order of work

The templates set run sequence from 0 to 20. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER
- 1 — Terms
- 2 — Sales rep
- 3 — CRM Account
- 4 — Customer
- 5 — Sales order
- 6 — Sales order line
- 7 — Invoice
- 8 — Invoice line
- 10 — Item inventory
- 15 — Contact
- 16 — Product
- 17 — Opportunity
- 18 — Opportunity line item
- 19 — Quote
- 20 — Quote line item

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- CRM account feed reads sales rep sync output (generic name), user sync output
- contacts feed reads account sync output (generic name), customer sync output (generic short name),
  contact sync output (generic name); no template in this set writes account sync output (generic
  name), customer sync output (generic short name), contact sync output (generic name)
- opportunity feed reads account sync output (generic name), opportunity sync output (generic name);
  no template in this set writes account sync output (generic name), opportunity sync output
  (generic name)
- opportunity line item feed reads product sync output (generic name), opportunity sync output
  (generic name), opportunity line item sync output (generic name); no template in this set writes
  product sync output (generic name), opportunity sync output (generic name), opportunity line item
  sync output (generic name)
- quote feed reads account sync output (generic name), opportunity sync output (generic name), quote
  header sync output (generic name); no template in this set writes account sync output (generic
  name), opportunity sync output (generic name), quote header sync output (generic name)
- quote line item feed reads product sync output (generic name), quote header sync output (generic
  name), quote line item sync output (generic name); no template in this set writes product sync
  output (generic name), quote header sync output (generic name), quote line item sync output
  (generic name)
- customer feed reads CRM account sync output (generic name), sales rep sync output (generic name)
- item inventory feed reads item sync output (generic name); no template in this set writes item
  sync output (generic name)
- invoice line feed reads invoice sync output (generic name)
- invoice feed reads CRM account sync output (generic name), customer sync output (generic name)
- product feed reads product sync output (generic name); no template in this set writes product sync
  output (generic name)
- sales order line feed reads sales order sync output (generic name)
- sales order feed reads CRM account sync output (generic name), customer sync output (generic name)

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Terms | Commercient Terms Managed Custom Object | 12 | returned list identifier → Commercient external key (QuickBooks package), Edit sequence → Commercient edit sequence, Name → Commercient name, Standard due days → Commercient standard due days, Standard discount days → Commercient standard discount days |
| Sales rep | Commercient Sales Rep Managed Custom Object | 8 | returned list identifier → Commercient external key (QuickBooks package), Sales rep full name → Commercient sales rep full name, Name → Commercient name, Edit sequence → Commercient edit sequence, Initial → Commercient initial |
| CRM Account | account | 13 | returned list identifier → Commercient AR customer code, Full name → name, Phone → Phone, Bill address city → Billing city, Billing address state → Billing state |
| Customer | Commercient Customer Managed Custom Object | 79 | returned list identifier → Commercient external key (QuickBooks package), Name → Name, Balance → Balance, Billing address line 1 → Billing address line 1, Billing address line 2 → Billing address line 2 |
| Sales order | Commercient Sales Order Managed Custom Object | 64 | returned transaction identifier → Commercient external key (QuickBooks package), Reference number → Commercient reference number, Billing address line 1 → Commercient billing address line 1, Billing address line 2 → Commercient billing address line 2, Billing address line 3 → Commercient billing address line 3 |
| Sales order line | Commercient Sales Order Line Managed Custom Object | 56 | Transaction line identifier → Commercient external key (QuickBooks package), returned transaction identifier → Commercient name, Billing address line 1 → Commercient billing address line 1, Billing address line 2 → Commercient billing address line 2, Billing address line 3 → Commercient billing address line 3 |
| Invoice | Commercient Invoice Managed Custom Object | 72 | returned transaction identifier → Commercient external key (QuickBooks package), returned transaction identifier → Commercient name, Reference number → Commercient reference number, Billing address line 1 → Commercient billing address line 1, Billing address line 2 → Commercient billing address line 2 |
| Invoice line | Commercient Invoice Line Managed Custom Object | 62 | Transaction line identifier → Commercient external key (QuickBooks package), Transaction line identifier → Commercient name, Applied amount → Commercient applied amount, Balance remaining → Commercient balance remaining, Balance remaining in home currency → Commercient balance remaining in home currency |
| Item inventory | Commercient QuickBooks Item Inventory Managed Custom Object | 34 | returned list identifier → Commercient external key (QuickBooks package), Name → Name, Asset account full name → Asset account full name, Asset account list identifier → Asset account list identifier, Barcode value → Barcode value |
| Contact | Contact | 8 | returned list identifier → External key (custom field), Given name, Family name → Name, Salutation → Salutation, Given name → Given name, Family name → Family name |
| Product | Product | 5 | returned list identifier → Commercient external key (earlier package), Name → Product code, Full name → Description, Name → Name, Active → Active |
| Opportunity | Opportunity | 7 | Customer list identifier → account lookup, returned transaction identifier → Name, returned transaction identifier → Opportunity number, Transaction date → close date property, Total amount → Amount |
| Opportunity line item | Opportunity line item | 10 | Line item list identifier → product lookup, returned transaction identifier → Opportunity, Transaction line identifier → external key column, Line amount → Unit price, Line quantity → Quantity |
| Quote | Quote | 17 | returned transaction identifier → Opportunity identifier, Customer list identifier → account lookup, Status → Status, Due date → Expiry date, Total amount → Grand total |
| Quote line item | Quote line item | 9 | Transaction line identifier → External key (custom field), Line item list identifier → product lookup, returned transaction identifier → Quote, Line quantity → Quantity, Transaction date → Service date |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/quickbooks-desktop`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped QuickBooks Desktop → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/quickbooks-desktop`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
