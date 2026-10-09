---
name: dlake-crmpro-salesforce/erps/sage-300-cre
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 300 CRE → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Sage 300 CRE: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-300-cre` (or `list_skills`) against the
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
| **Active Contract record** | The templates push Commercient Active Contract Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Active Contract Managed Custom Object | active contracts |
| **Master job part 1** | The templates push Commercient Master Job Part 1 Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Master Job Part 1 Managed Custom Object | jobs |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient Master AR Customer Managed Custom Object | customers, service sites |
| **Product** | The templates push Commercient Active Contract Item Managed Custom Object, Commercient Billed Invoice Item Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Active Contract Item Managed Custom Object, Commercient Billed Invoice Item Managed Custom Object | active contract items, billed invoice items |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Billed Invoice Managed Custom Object | billed invoices |
| **Open AR Invoice Header** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Current AR Transaction Managed Custom Object | current AR transactions |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Account | Account | Commercient AR customer code | 1 |
| Customer | Commercient Master AR Customer Managed Custom Object | Commercient external key | 2 |
| Customer Account Reverse Lookup | Account | Commercient AR customer code | 3 |
| Current AR transaction | Commercient Current AR Transaction Managed Custom Object | Commercient external key | 4 |
| Active Contract record | Commercient Active Contract Managed Custom Object | Commercient external key | 5 |
| Master job part 1 | Commercient Master Job Part 1 Managed Custom Object | Commercient external key | 6 |
| Active contract item | Commercient Active Contract Item Managed Custom Object | Commercient external key | 7 |
| Billed invoice | Commercient Billed Invoice Managed Custom Object | Commercient external key | 8 |
| Billed invoice item | Commercient Billed Invoice Item Managed Custom Object | Commercient external key | 9 |
| Sync Contact records | Contact | Commercient external key | 12 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | customers |
| customer master feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| current AR transaction feed | insert + update | current AR transactions |
| active contract feed | insert + update | active contracts |
| master job part 1 feed | insert + update | jobs |
| active contract item feed | insert + update | active contract items |
| billed invoice feed | insert + update | billed invoices |
| billed invoice item feed | insert + update | billed invoice items |
| contact feed | insert + update | service sites |

## 4. Order of work

The templates set run sequence from 1 to 12. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Account
- 2 — Customer
- 3 — Customer Account Reverse Lookup
- 4 — Current AR transaction
- 5 — Active Contract record
- 6 — Master job part 1
- 7 — Active contract item
- 8 — Billed invoice
- 9 — Billed invoice item
- 12 — Sync Contact records

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- master job part 1 feed reads account sync output, customer master sync output, active contract
  sync output
- customer account lookup feed reads customer master sync output
- contact feed reads account sync output, child account sync output; no template in this set writes
  child account sync output
- customer master feed reads account sync output
- active contract item feed reads account sync output, customer master sync output, active contract
  sync output, master job part 1 sync output
- billed invoice item feed reads account sync output, customer master sync output, master job part 1
  sync output, active contract sync output
- billed invoice feed reads account sync output, customer master sync output, active contract sync
  output
- current AR transaction feed reads customer master sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Account | Account | 12 | Customer → Commercient AR customer code, Customer → Name, Address 1, Address 2, Address 3, Address line 4 → Shipping street, City → Shipping city, State → Shipping state |
| Customer | Commercient Master AR Customer Managed Custom Object | 87 | Customer → Commercient external key, Customer → Commercient name, Address book company identifier → Commercient address book company identifier, Australian business number → Commercient Australian business number, Add on table → Commercient add on table |
| Customer Account Reverse Lookup | Account | 2 | Customer → Commercient AR customer code, the linked Salesforce record → Commercient Sage 300 Construction and Real Estate customer master (related record) |
| Current AR transaction | Commercient Current AR Transaction Managed Custom Object | 74 | Run, Sequence → Commercient external key, Run, Sequence → Commercient name, Accounting date → Commercient accounting date, Activity sequence → Commercient activity sequence, Adjustment → Commercient adjustment |
| Active Contract record | Commercient Active Contract Managed Custom Object | 50 | Contract → Commercient external key, Contract → Commercient name, Leading account segment → Commercient leading account segment, Add stored materials → Commercient add stored materials, Add on table → Commercient add on table |
| Master job part 1 | Commercient Master Job Part 1 Managed Custom Object | 87 | Job → Commercient external key, Job → Commercient name, Actual completion date → Commercient actual completion date, Actual start date → Commercient actual start date, Address 1 → Commercient address 1 |
| Active contract item | Commercient Active Contract Item Managed Custom Object | 76 | Contract, Contract item → Commercient external key, Contract, Contract item → Commercient name, Leading account segment → Commercient leading account segment, Accounts receivable account → Commercient accounts receivable account, Approval date → Commercient approval date |
| Billed invoice | Commercient Billed Invoice Managed Custom Object | 64 | Invoice → Commercient external key, Invoice → Commercient name, Accounting date → Commercient accounting date, Add on → Commercient add on, Adjustment to date → Commercient adjustment to date |
| Billed invoice item | Commercient Billed Invoice Item Managed Custom Object | 37 | Add on → Commercient add on, Add on percent → Commercient add on percent, Add on type → Commercient add on type, Amount → Commercient amount, Contract → Commercient contract |
| Sync Contact records | Contact | 9 | Commercient external key → Commercient external key, account lookup → account lookup, Given name → Given name, Family name → Family name, Email → Email |

## 6. Community templates

The catalogue carries 81 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 81
- Default operations: insert on 81, update on 81, delete on 81
- Marked as circular sync: 0
- Licence groups they span: 9
- Destination objects: Account, Commercient Master AR Customer Managed Custom Object, Commercient
  Master Job Part 1 Managed Custom Object, Commercient Active Contract Managed Custom Object,
  Commercient Active Contract Item Managed Custom Object, Commercient Billed Invoice Managed Custom
  Object, Commercient Billed Invoice Item Managed Custom Object, Commercient Current AR Transaction
  Managed Custom Object, Contact, Commercient Master Job Part 2 Managed Custom Object, Job (custom
  object), Commercient Master Standard Cost Code Managed Custom Object, Invoice (custom object),
  Opportunity, Production job (custom object), Project (custom object), Sage 300 Construction and
  Real Estate master job cost category (custom object) and 9 custom objects
- Object display names: Customer, Customer Account Reverse Lookup, Account, Active Contract record,
  Active contract item, Billed invoice, Billed invoice item, Master job part 1, Contact, Current AR
  transaction, Sync Account, Sync Billed invoice, 38 more and 7 further templates
- Template groups: Account, Product, Invoice, CRM Opportunity and Line, Sales order

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-300-cre`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 300 CRE → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/sage-300-cre`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
