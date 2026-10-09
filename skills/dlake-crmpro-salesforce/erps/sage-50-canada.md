---
name: dlake-crmpro-salesforce/erps/sage-50-canada
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 50 Canada → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Sage 50 Canada: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-50-canada` (or `list_skills`) against the
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
| **GET USER** | The templates push users to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | users | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient Sage 50 Canada Customer Managed Custom Object | customers, customer shipping addresses |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sage 50 Canada Address Managed Custom Object | customer shipping addresses, customers |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sage 50 Canada Invoice Managed Custom Object, Commercient Sage 50 Canada Invoice Detail Managed Custom Object | invoice headers, customers, invoice lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sage 50 Canada Sales Order Managed Custom Object, Commercient Sage 50 Canada Sales Order Line Managed Custom Object | sales order headers, sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| Account | Account | Commercient AR customer code | 2 |
| Sage 50 Canada Customer | Commercient Sage 50 Canada Customer Managed Custom Object | Commercient external key | 3 |
| Sage 50 Canada Customer to account lookup | Account | Commercient AR customer code | 4 |
| Sage 50 Canada Shipping address | Commercient Sage 50 Canada Address Managed Custom Object | Commercient external key | 5 |
| Sage 50 Canada Sales order header | Commercient Sage 50 Canada Sales Order Managed Custom Object | Commercient external key | 6 |
| Sage 50 Canada Sales order detail | Commercient Sage 50 Canada Sales Order Line Managed Custom Object | Commercient external key | 7 |
| Sage 50 Canada Invoice header | Commercient Sage 50 Canada Invoice Managed Custom Object | Commercient external key | 8 |
| Sage 50 Canada Invoice detail | Commercient Sage 50 Canada Invoice Detail Managed Custom Object | Commercient external key | 9 |
| Contact | Contact | External key (custom field) | 10 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert only | customers, customer shipping addresses |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| shipping address feed | insert + update | customer shipping addresses, customers |
| sales order feed | insert + update | sales order headers |
| sales order line feed | insert + update | sales order lines |
| invoice feed | insert + update | invoice headers, customers |
| invoice line feed | insert + update | invoice lines |
| contact feed | insert + update | customers |

## 4. Order of work

The templates set run sequence from 0 to 10. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER
- 2 — Account
- 3 — Sage 50 Canada Customer
- 4 — Sage 50 Canada Customer to account lookup
- 5 — Sage 50 Canada Shipping address
- 6 — Sage 50 Canada Sales order header
- 7 — Sage 50 Canada Sales order detail
- 8 — Sage 50 Canada Invoice header
- 9 — Sage 50 Canada Invoice detail
- 10 — Contact

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- customer account lookup feed reads customer sync output
- contact feed reads account sync output
- customer feed reads account sync output
- shipping address feed reads account sync output, customer sync output, retrieved owner sync
  output; no template in this set writes retrieved owner sync output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads invoice sync output
- sales order feed reads account sync output, customer sync output
- sales order line feed reads sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Account | Account | 12 | Commercient AR customer code column → Commercient AR customer code, Billing street → Billing street, Billing city → Billing city, Billing state → Billing state, Billing country → Billing country |
| Sage 50 Canada Customer | Commercient Sage 50 Canada Customer Managed Custom Object | 62 | Account → Account, Inactive flag → Inactive flag, Can save credit card (ERP) → Can save credit card (ERP), Electronic funds transfer my code flag → Electronic funds transfer my code flag, Electronic funds transfer my reference flag → Commercient electronic funds transfer my reference flag |
| Sage 50 Canada Customer to account lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Commercient Sage 50 Canada Customer Managed Custom Object → Commercient Sage 50 Canada Customer Managed Custom Object |
| Sage 50 Canada Shipping address | Commercient Sage 50 Canada Address Managed Custom Object | 19 | external key column → Commercient external key, Account → Account, Sage 50 Canada customer (related record) → Commercient Sage 50 Canada Customer Managed Custom Object, Customer record number → Commercient customer record number, Internal record number → Commercient internal record number |
| Sage 50 Canada Sales order header | Commercient Sage 50 Canada Sales Order Managed Custom Object | 59 | Account → Account, Customer → Commercient customer (related record), Ledger account number → Ledger account number, Cheque identifier → Commercient cheque identifier, Cheque number → Commercient cheque number |
| Sage 50 Canada Sales order detail | Commercient Sage 50 Canada Sales Order Line Managed Custom Object | 25 | sales order reference → sales order reference (custom field), Ledger account identifier → Ledger account identifier, Line amount → Commercient line amount, Default base price (ERP) → Default base price (ERP), Ledger account department identifier → Ledger account department identifier |
| Sage 50 Canada Invoice header | Commercient Sage 50 Canada Invoice Managed Custom Object | 36 | customer reference → customer reference (custom field), Account → Account, Ledger account identifier → Ledger account identifier (custom field), Allocate to all (ERP) → Allocate to all (custom field), Address identifier → Address identifier (custom field) |
| Sage 50 Canada Invoice detail | Commercient Sage 50 Canada Invoice Detail Managed Custom Object | 25 | invoice reference → invoice reference (custom field), Base price → Base price (custom field), Line description → Line description (custom field), →, Duty amount → Duty amount (custom field) |
| Contact | Contact | 11 | account lookup → account lookup, Family name → Family name, Given name → Given name, Email → Email, Phone → Phone |

## 6. Community templates

The catalogue carries 41 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 41
- Default operations: insert on 41, update on 41, delete on 41
- Marked as circular sync: 1
- Licence groups they span: 10
- Destination objects: Account, Contact, Opportunity, Product, Commercient Sage 50 Canada Customer
  Managed Custom Object, Commercient Account Matching Managed Custom Object, Commercient Contact
  Matching Managed Custom Object, Commercient Product Matching Managed Custom Object, Commercient
  Sage 50 Canada Address Managed Custom Object, Commercient Sage 50 Canada Item Managed Custom
  Object, Commercient Sage 50 Canada Sales Order Managed Custom Object, Commercient Sage 50 Canada
  Sales Order Line Managed Custom Object, Commercient Sage 50 Canada Warehouse Managed Custom
  Object, Opportunity line item, Price book entry, Temp (custom object) and 11 custom objects
- Object display names: Account, Item Master, Sage 50 Canada Customer to account lookup, Product,
  Sage 50 Canada Customer, Sage 50 Canada Invoice detail, Sage 50 Canada Invoice header, Sage 50
  Canada Sales order detail, Sage 50 Canada Sales order header, Sage 50 Canada Shipping address,
  Warehouse, Account Update, 14 more and a further template
- Template groups: Account, Product, CRM Opportunity and Line, Invoice, Sales order, Customer Multi
  Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-50-canada`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 50 Canada → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/sage-50-canada`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
