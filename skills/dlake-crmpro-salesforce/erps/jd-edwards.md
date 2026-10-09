---
name: dlake-crmpro-salesforce/erps/jd-edwards
kind: erp-summary
description: >-
  Use it when standing up or reading a JD Edwards → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — JD Edwards: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/jd-edwards` (or `list_skills`) against the
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
| **Get Users** | The templates push User to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | User | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Commercient Customer Master Managed Custom Object, Commercient User Defined Codes Managed Custom Object | address book records, customer master records, address book addresses, address book phone numbers, user defined codes |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Order Address Managed Custom Object, Commercient Address Book Master Managed Custom Object, Commercient Address Book Phone Number Managed Custom Object | order addresses, address book records, customer master records, address book phone numbers |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient JD Edwards Invoice Header Managed Custom Object, Commercient JD Edwards Invoice Details Managed Custom Object | receipt headers, customer master records, receipt lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sales Order Header Managed Custom Object, Commercient Sales Order Detail Managed Custom Object | sales order headers, customer master records, sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Get Users | User | — | 0 |
| JD Edwards Salesperson | Commercient User Defined Codes Managed Custom Object | External key (custom field) | 1 |
| Account | Account | Commercient AR customer code | 2 |
| JD Edwards Customer | Commercient Customer Master Managed Custom Object | Commercient external key (JD Edwards package) | 3 |
| Account customer lookup | Account | Commercient AR customer code | 5 |
| JD Edwards Sales order | Commercient Sales Order Header Managed Custom Object | Commercient external key (JD Edwards package) | 6 |
| JD Edwards Sales order line | Commercient Sales Order Detail Managed Custom Object | Commercient external key (JD Edwards package) | 7 |
| JD Edwards Invoice Header | Commercient JD Edwards Invoice Header Managed Custom Object | External key (custom field) | 8 |
| JD Edwards Invoice Details | Commercient JD Edwards Invoice Details Managed Custom Object | External key (custom field) | 9 |
| JD Edwards Order Address | Commercient Order Address Managed Custom Object | External key (custom field) | 17 |
| JD Edwards Sales order History | Commercient Sales Order Header Managed Custom Object | Commercient external key (JD Edwards package) | 18 |
| JD Edwards Sales order line History | Commercient Sales Order Detail Managed Custom Object | Commercient external key (JD Edwards package) | 19 |
| JD Edwards Address book master | Commercient Address Book Master Managed Custom Object | Commercient external key (JD Edwards package) | 20 |
| JD Edwards Address book phone number | Commercient Address Book Phone Number Managed Custom Object | Commercient external key (JD Edwards package) | 21 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | user defined codes |
| account feed | insert + update | address book records, customer master records, address book addresses, address book phone numbers |
| customer feed | insert + update | customer master records, address book records |
| customer account lookup feed | insert + update | address book records, customer master records |
| sales order feed | insert + update | sales order headers, customer master records |
| sales order line feed | insert + update | sales order lines |
| invoice feed | insert + update | receipt headers, customer master records |
| invoice line feed | insert + update | receipt lines |
| order address feed | insert + update | order addresses |
| sales order feed | insert only | sales order headers, customer master records |
| sales order line feed | insert + update | sales order lines |
| address book master feed | insert + update | address book records, customer master records |
| address book phone number feed | insert + update | address book phone numbers, customer master records |

## 4. Order of work

The templates set run sequence from 0 to 21. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — Get Users
- 1 — JD Edwards Salesperson
- 2 — Account
- 3 — JD Edwards Customer
- 5 — Account customer lookup
- 6 — JD Edwards Sales order
- 7 — JD Edwards Sales order line
- 8 — JD Edwards Invoice Header
- 9 — JD Edwards Invoice Details
- 17 — JD Edwards Order Address
- 18 — JD Edwards Sales order History
- 19 — JD Edwards Sales order line History
- 20 — JD Edwards Address book master
- 21 — JD Edwards Address book phone number

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads payment terms sync output; no template in this set writes payment terms sync
  output
- customer account lookup feed reads customer sync output
- customer feed reads account sync output
- order address feed reads sales order sync output
- address book master feed reads account sync output, customer sync output
- address book phone number feed reads account sync output, customer sync output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads invoice sync output
- sales order feed reads account sync output, customer sync output, payment terms sync output; no
  template in this set writes payment terms sync output
- sales order line feed reads sales order sync output
- sales order feed reads account sync output, customer sync output, payment terms sync output; no
  template in this set writes payment terms sync output
- sales order line feed reads sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| JD Edwards Salesperson | Commercient User Defined Codes Managed Custom Object | 13 | System code → System code (custom field), Code type → Code type (custom field), Code value → Code value (custom field), Description → Description (custom field), Second description → Second description (custom field) |
| Account | Account | 16 | Commercient AR customer code column → Commercient AR customer code, Phone → Phone, Billing street → Billing street, Billing postal code → Billing postal code, Billing city → Billing city |
| JD Edwards Customer | Commercient Customer Master Managed Custom Object | 232 | Margin classification code → Margin classification code, Average days classification code → Average days classification code, Sales classification code → Sales classification code, →, Address number → Commercient address number |
| Account customer lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, linked JD Edwards customer master → JD Edwards customer master (custom field) |
| JD Edwards Sales order | Commercient Sales Order Header Managed Custom Object | 136 | Account → Account, linked JD Edwards customer master → linked JD Edwards customer master, Payment terms code → Payment terms code, →, → |
| JD Edwards Sales order line | Commercient Sales Order Detail Managed Custom Object | 269 | →, Sold to number → Commercient sold to number, →, →, Second item number → Commercient second item number |
| JD Edwards Invoice Header | Commercient JD Edwards Invoice Header Managed Custom Object | 99 | Header payment identifier → Header payment identifier (custom field), Receipt number → Receipt number (custom field), Receipt address number → Receipt address number (custom field), Payor address number → Payor address number (custom field), Receipt date → Receipt date (custom field) |
| JD Edwards Invoice Details | Commercient JD Edwards Invoice Details Managed Custom Object | 200 | linked receipt header → Linked receipt header (custom field), linked receipt header → Linked receipt header (custom field), Detail payment identifier → Detail payment identifier (custom field), Detail payment identifier → Detail payment identifier (custom field), File line identifier → File line identifier |
| JD Edwards Order Address | Commercient Order Address Managed Custom Object | 23 | linked sales order header → Linked sales order header (custom field), Order address order number → Order address order number (custom field), Order address order type → Order address order type (custom field), Order address order company → Order address order company (custom field), Address type → Address type (custom field) |
| JD Edwards Sales order History | Commercient Sales Order Header Managed Custom Object | 136 | Account → Account, linked JD Edwards customer master → linked JD Edwards customer master, Payment terms code → Payment terms code, →, → |
| JD Edwards Sales order line History | Commercient Sales Order Detail Managed Custom Object | 269 | →, Sold to number → Commercient sold to number, →, →, Second item number → Commercient second item number |
| JD Edwards Address book master | Commercient Address Book Master Managed Custom Object | 98 | Effective date → Commercient effective date, Date scheduled in → Commercient date scheduled in, User reserved date → Commercient user reserved date, Date updated → Commercient date updated, Time last updated → Commercient time last updated |
| JD Edwards Address book phone number | Commercient Address Book Phone Number Managed Custom Object | 19 | Phone record address number → Commercient phone record address number, Who's who line number → Commercient who's who line number, Phone line number → Phone line number, Contact line number → Contact line number, Phone type → Commercient phone type |

## 6. Community templates

The catalogue carries 79 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 79
- Default operations: insert on 79, update on 79, delete on 79
- Marked as circular sync: 0
- Licence groups they span: 13
- Destination objects: Account, Product, Commercient Sales Order Header Managed Custom Object,
  Commercient Sales Order Detail Managed Custom Object, Price book entry, Commercient JD Edwards
  Invoice Header Managed Custom Object, Commercient JD Edwards Invoice Details Managed Custom
  Object, Commercient Address Book Master Managed Custom Object, Commercient User Defined Codes
  Managed Custom Object, Commercient Order Address Managed Custom Object, Contact, Opportunity,
  User, Commercient Address Book Phone Number Managed Custom Object, Custom Pricing (custom object),
  JD Edwards Invoice Detail (custom object), JD Edwards Invoice Header (custom object) and 10 custom
  objects
- Object display names: Account, Account customer lookup, JD Edwards Customer, JD Edwards Invoice
  Header, JD Edwards Sales order, JD Edwards Sales order line, Product, JD Edwards Invoice Details,
  JD Edwards Item Master, Product Reverse Lookup, Contact, Create Standard Price Book, 26 more and 4
  further templates
- Template groups: Account, Product, Sales order, Invoice, Customer Multi Ship Addresses, CRM
  Opportunity and Line, Opportunity

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/jd-edwards`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped JD Edwards → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/jd-edwards`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
