---
name: dlake-crmpro-salesforce/erps/quickbooks-online
kind: erp-summary
description: >-
  Use it when standing up or reading a QuickBooks Online → Salesforce template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a child of,
  which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — QuickBooks Online: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/quickbooks-online` (or `list_skills`) against
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
| **Quickbook Online Payment** | ERP Payment data becomes Commercient QuickBooks Online Payment Managed Custom Object in Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient QuickBooks Online Payment Managed Custom Object | payments |
| **Quickbook Online payment line** | ERP payment line data becomes Commercient QuickBooks Online Payment Line Managed Custom Object in Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient QuickBooks Online Payment Line Managed Custom Object | payment lines |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient QuickBooks Online Customer Managed Custom Object | customers |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient QuickBooks Online Invoice Managed Custom Object, Commercient QuickBooks Online Invoice Line Managed Custom Object | invoices, linked payment transactions, payments, invoice details, invoice lines |
| **Invoice History Headers** | The Invoices from the ERP invoice module are synchronized to the Commercient Invoice Header (MCO) object in CRM. Customer service and sales people can visualize the status of the Invoice such as open, closed, as well as the balance remaining and the due date. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient QuickBooks Online Invoice Managed Custom Object, Commercient QuickBooks Online Invoice Line Managed Custom Object | invoices, linked payment transactions, Invoice, invoice lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Sync Account | Account | Commercient AR customer code | 1 |
| QuickBooks Online Customer | Commercient QuickBooks Online Customer Managed Custom Object | Commercient external key (QuickBooks package) | 2 |
| Quickbook Online Account Reverse Lookup | Account | Commercient AR customer code | 3 |
| Quickbook Online Invoice | Commercient QuickBooks Online Invoice Managed Custom Object | Commercient external key (QuickBooks package) | 5 |
| Quickbook Online invoice line | Commercient QuickBooks Online Invoice Line Managed Custom Object | Commercient external key 1 | 6 |
| Quickbook Online Payment | Commercient QuickBooks Online Payment Managed Custom Object | Commercient external key (QuickBooks package) | 7 |
| Quickbook Online payment line | Commercient QuickBooks Online Payment Line Managed Custom Object | Commercient external key (QuickBooks package) | 8 |
| Invoice History Header | Commercient QuickBooks Online Invoice Managed Custom Object | Commercient external key (QuickBooks package) | 10 |
| Invoice History Detail | Commercient QuickBooks Online Invoice Line Managed Custom Object | Commercient external key 1 | 11 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| contact feed | insert + update | customers |
| account feed | insert + update | customers |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| invoice feed | insert + update | invoices, linked payment transactions, payments, invoice details |
| invoice line feed | insert + update | invoice lines |
| payment feed | insert + update | payments |
| payment line feed | insert + update | payment lines |
| invoice history feed | insert + update | invoices, linked payment transactions, Invoice |
| invoice history line feed | insert + update | invoice lines, invoices |

## 4. Order of work

The templates set run sequence from 1 to 11. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Sync Account
- 2 — QuickBooks Online Customer
- 3 — Quickbook Online Account Reverse Lookup
- 5 — Quickbook Online Invoice
- 6 — Quickbook Online invoice line
- 7 — Quickbook Online Payment
- 8 — Quickbook Online payment line
- 10 — Invoice History Header
- 11 — Invoice History Detail

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- payment feed reads account sync output, customer sync output, payment sync output; no template in
  this set writes customer sync output, payment sync output
- payment line feed reads payment sync output, payment line sync output; no template in this set
  writes payment sync output, payment line sync output
- customer account lookup feed reads customer sync output; no template in this set writes customer
  sync output
- contact feed reads account sync output, customer sync output, contact sync output; no template in
  this set writes customer sync output, contact sync output
- customer feed reads account sync output, customer sync output; no template in this set writes
  customer sync output
- invoice feed reads account sync output, customer sync output, invoice sync output; no template in
  this set writes customer sync output, invoice sync output
- invoice line feed reads invoice sync output, invoice line sync output; no template in this set
  writes invoice sync output, invoice line sync output
- invoice history feed reads account sync output, customer sync output; no template in this set
  writes customer sync output
- invoice history line feed reads invoice history sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Contact | Contact | 6 | Given name → Given name, Middle name → Middle name, Family name → Family name, Title → Title, account lookup → account lookup |
| Sync Account | Account | 10 | Commercient AR customer code column → Commercient AR customer code, Billing street → Billing street, Billing city → Billing city, Billing country → Billing country, Billing postal code → Billing postal code |
| QuickBooks Online Customer | Commercient QuickBooks Online Customer Managed Custom Object | 25 | Balance → Commercient balance, Bill address city → Commercient billing city, Billing address country → Commercient billing country, Billing address line 1 → Commercient billing address line 1, Billing address line 2 → Commercient billing address line 2 |
| Quickbook Online Account Reverse Lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Commercient Customer Managed Custom Object → Commercient Customer Managed Custom Object |
| Quickbook Online Invoice | Commercient QuickBooks Online Invoice Managed Custom Object | 37 | AR account reference identifier → Commercient AR account reference identifier, AR account reference full name → Commercient AR account reference full name, Bill address city → Commercient billing city, Billing address country → Commercient billing country, Billing address line 1 → Commercient billing address line 1 |
| Quickbook Online invoice line | Commercient QuickBooks Online Invoice Line Managed Custom Object | 15 | external key 1 column → Commercient external key 1, Invoice line amount → Commercient invoice line amount, Invoice line class identifier → Commercient invoice line class identifier, Invoice line class full name → Commercient invoice line class full name, Invoice line description → Commercient invoice line description |
| Quickbook Online Payment | Commercient QuickBooks Online Payment Managed Custom Object | 20 | AR account reference identifier → Commercient AR account reference identifier, AR account reference full name → Commercient AR account reference full name, Currency reference identifier → Commercient currency reference identifier, Currency reference full name → Commercient currency reference full name, Customer list identifier → Commercient customer reference identifier |
| Quickbook Online payment line | Commercient QuickBooks Online Payment Line Managed Custom Object | 15 | Payment identifier → payment identifier (custom field), Balance → balance (custom field), Class reference name → class reference name (custom field), Class reference type → class reference type (custom field), Class reference value → class reference value (custom field) |
| Invoice History Header | Commercient QuickBooks Online Invoice Managed Custom Object | 119 | Deposit → Commercient deposit, Allow Intuit payment network payment → Commercient allow Intuit payment network payment, Allow online payment → Commercient allow online payment, Allow online credit card payment → Allow online credit card payment, Allow online bank transfer payment → Allow online bank transfer payment |
| Invoice History Detail | Commercient QuickBooks Online Invoice Line Managed Custom Object | 38 | external key 1 column → Commercient external key 1, returned invoice identifier → returned invoice identifier, Discount amount → Discount amount, Discount rate → Discount rate, Service date → Service date |

## 6. Community templates

The catalogue carries 56 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 56
- Default operations: insert on 56, update on 56, delete on 56
- Marked as circular sync: 0
- Licence groups they span: 11
- Destination objects: Account, Commercient QuickBooks Online Invoice Line Managed Custom Object,
  Commercient QuickBooks Online Invoice Managed Custom Object, Commercient QuickBooks Online
  Customer Managed Custom Object, Commercient QuickBooks Online Payment Managed Custom Object,
  Commercient QuickBooks Online Payment Line Managed Custom Object, Contact, QuickBooks Online AR
  Invoice Payment (custom object), account, Commercient Account Matching Managed Custom Object,
  Invoice (custom object), Product, QuickBooks Online Open AR Invoice Detail (custom object),
  Commercient Product Matching Managed Custom Object, Commercient QuickBooks Online Terms Managed
  Custom Object, Commercient QuickBooks Online Item (custom object), Invoice Line Items (custom
  object), Payment (custom object), QuickBooks Online Open AR Invoice Header (custom object), 2 more
  and a custom object
- Object display names: QuickBooks Online Customer, Quickbook Online Invoice, Quickbook Online
  invoice line, Quickbook Online Payment, Sync Open AR invoice record sync output (generic name)
  Detail, Account, Quickbook Online Account Reverse Lookup, Quickbook Online payment line, Sync
  account matching, Sync AR invoice record sync output (generic name) payment records, Sync Contact,
  Sync invoice record sync output (generic name) History Detail, 24 more and 2 further templates
- Template groups: Account, Invoice, Open AR Invoice Header, Invoice History Headers, Product, AR
  Invoice Payments

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/quickbooks-online`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped QuickBooks Online → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/quickbooks-online`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
