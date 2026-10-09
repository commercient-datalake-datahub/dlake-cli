---
name: dlake-crmpro-salesforce/erps/xero
kind: erp-summary
description: >-
  Use it when standing up or reading a Xero → Salesforce template set, when deciding which templates
  to import and activate, or when a run completes without pushing records and the answer is in the
  view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro generally,
  and dlake-crmpro-salesforce, the destination skill this page is a child of, which carries the
  Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Xero: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/xero` (or `list_skills`) against the Commercient
admin plane. Existing customers who need access or help: contact support@commercient.com. New
customers: contact sales@commercient.com to become a customer and be whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-salesforce is the
destination skill this page is a child of, and the authority for the Salesforce conventions that
hold across every ERP: read it first, then come back here for what this source's own templates set.
This page grows as the catalogue does.

## 1. What the templates deliver

| Group | Business outcome | Objects | Source tables and views |
|---|---|---|---|
| **Get Salesforce User** | The templates push User to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | User | — |
| **Document Sync - Xero Invoices** | ERP Invoices, Invoice attachments (Xero table) data becomes Content document in Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Content document | invoice records, invoice attachments |
| **Xero Credit Notes** | ERP Contact, Credit notes (Xero table), Credit note contacts (Xero table) data becomes Commercient Invoices Credit Notes Managed Custom Object in Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Invoices Credit Notes Managed Custom Object | credit notes, credit note payments, credit note contacts, contacts, invoice credit notes |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient Xero Contact Managed Custom Object | contacts, contact addresses, contact phone numbers, contact sales tracking categories, contact accounts payable details, contact accounts receivable details |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Xero Address Managed Custom Object | contact addresses, contacts |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Xero Invoice Managed Custom Object, Commercient Xero Line Item Managed Custom Object, Commercient Xero Payment Managed Custom Object, Commercient Invoices Credit Notes Managed Custom Object | invoice records, invoice contacts, contacts, invoice line items, invoice payments, payments |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Get Salesforce User | User | Commercient salesperson code | 0 |
| Account | Account | Commercient AR customer code | 1 |
| Xero Customer | Commercient Xero Contact Managed Custom Object | Commercient external key | 2 |
| Xero Customer to account lookup | Account | Commercient AR customer code | 3 |
| Xero Address | Commercient Xero Address Managed Custom Object | Commercient external key | 4 |
| Xero Invoice | Commercient Xero Invoice Managed Custom Object | Commercient external key | 5 |
| Xero Invoice line item | Commercient Xero Line Item Managed Custom Object | Commercient external key | 6 |
| Xero Invoice payment | Commercient Xero Payment Managed Custom Object | Commercient external key | 7 |
| Document Sync - Xero Invoices | Content document | — | 8 |
| Contact | Contact | External key (custom field) | 10 |
| Xero Invoice Credit Notes | Commercient Invoices Credit Notes Managed Custom Object | Commercient external key | 11 |
| Xero Credit Notes | Commercient Invoices Credit Notes Managed Custom Object | Commercient external key | 12 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | contacts, contact addresses, contact phone numbers, contact sales tracking categories |
| customer feed | insert + update | contacts, contact accounts payable details, contact accounts receivable details |
| customer account lookup feed | insert + update | contacts |
| address feed | insert + update | contact addresses, contacts |
| invoice feed | insert + update | invoice records, invoice contacts, contacts |
| invoice line feed | insert + update | invoice line items, invoice records |
| invoice payment feed | insert + update | invoice payments, payments, invoice contacts, contacts |
| invoice document feed | insert + update | invoice records, invoice attachments |
| contact feed | insert + update | contacts, contact phone numbers, contact addresses |
| invoice credit note feed | insert + update | invoice credit notes, invoice records, invoice contacts, contacts |
| credit note feed | insert + update | credit notes, credit note payments, credit note contacts, contacts, invoice credit notes |

## 4. Order of work

The templates set run sequence from 0 to 12. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — Get Salesforce User
- 1 — Account
- 2 — Xero Customer
- 3 — Xero Customer to account lookup
- 4 — Xero Address
- 5 — Xero Invoice
- 6 — Xero Invoice line item
- 7 — Xero Invoice payment
- 8 — Document Sync - Xero Invoices
- 10 — Contact
- 11 — Xero Invoice Credit Notes
- 12 — Xero Credit Notes

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- invoice document feed reads invoice sync output; no template in this set writes
- credit note feed reads account sync output, customer sync output
- account feed reads user sync output
- customer account lookup feed reads customer sync output, account sync output
- contact feed reads account sync output
- customer feed reads account sync output
- address feed reads account sync output, customer sync output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads invoice sync output
- invoice payment feed reads customer sync output, invoice sync output
- invoice credit note feed reads account sync output, customer sync output, invoice sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Account | Account | 13 | Commercient AR customer code column → Commercient AR customer code, Phone → Phone, Billing street → Billing street, Billing city → Billing city, Billing state code → Billing state code |
| Xero Customer | Commercient Xero Contact Managed Custom Object | 24 | Account → Account, Account number → Account number, Accounts payable tax type → Accounts payable tax type, Accounts receivable tax type → Accounts receivable tax type, AP balance outstanding → AP balance outstanding |
| Xero Customer to account lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Commercient Xero contact column → Commercient Xero Contact Managed Custom Object |
| Xero Address | Commercient Xero Address Managed Custom Object | 14 | Account → Account, Commercient Xero contact (related record) → Xero contact lookup (custom field), Address line 1 → Commercient address line 1, Address line 2 → Commercient address line 2, Address line 3 → Commercient address line 3 |
| Xero Invoice | Commercient Xero Invoice Managed Custom Object | 25 | Account → Account, Commercient Xero contact (related record) → Commercient Xero contact (related record), Amount credited → Amount credited, Amount due → Commercient amount due, Amount paid → Commercient amount paid |
| Xero Invoice line item | Commercient Xero Line Item Managed Custom Object | 13 | Commercient Xero invoice (related record) → Commercient Xero invoice (related record), Account code → Account code, Description → Description, Discount rate → Discount rate, Invoice identifier → Commercient invoice identifier |
| Xero Invoice payment | Commercient Xero Payment Managed Custom Object | 13 | Commercient Xero contact (related record) → Commercient Xero contact (related record), Invoice 1 → Invoice 1, Amount → Commercient amount, Bank amount → Commercient bank amount, Currency rate → Currency rate |
| Document Sync - Xero Invoices | Content document | 6 | record identifier → record identifier, File display name → File display name, Body → Body, Description → Description, parent account lookup → parent account lookup |
| Contact | Contact | 12 | Given name → Given name, Family name → Family name, account lookup → account lookup, Email → Email, Phone → Phone |
| Xero Invoice Credit Notes | Commercient Invoices Credit Notes Managed Custom Object | 13 | Account → Account, Commercient Xero contact (related record) → Commercient Xero contact (related record), Commercient Xero invoice (related record) → Commercient Xero invoice (related record), Credit note identifier → Commercient credit note identifier, Credit note number → Commercient credit note number |
| Xero Credit Notes | Commercient Invoices Credit Notes Managed Custom Object | 11 | Account → Account, Commercient Xero contact (related record) → Commercient Xero contact (related record), Credit note identifier → Commercient credit note identifier, Credit note number → Commercient credit note number, record identifier → Commercient record identifier |

## 6. Community templates

The catalogue carries 23 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 23
- Default operations: insert on 23, update on 23, delete on 23
- Marked as circular sync: 1
- Licence groups they span: 6
- Destination objects: Account, Commercient Invoices Credit Notes Managed Custom Object, Commercient
  Xero Address Managed Custom Object, Commercient Xero Contact Managed Custom Object, Commercient
  Xero Invoice Managed Custom Object, Commercient Xero Line Item Managed Custom Object, Commercient
  Xero Payment Managed Custom Object, Contact, Content document, User
- Object display names: Account, Contact, Document Sync - Xero Invoices, Xero Address, Xero Credit
  Notes, Xero Customer, Xero Customer to account lookup, Xero Invoice, Xero Invoice Credit Notes,
  Xero Invoice line item, Xero Invoice payment, Get Salesforce User
- Template groups: Account, Invoice, Customer Multi Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/xero`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Xero → Salesforce templates set up. dlake-crmpro-salesforce is the destination skill
this page sits under: its own text is the authority for the Salesforce conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/xero`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
