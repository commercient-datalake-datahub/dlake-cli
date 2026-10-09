---
name: dlake-crmpro-salesforce/erps/aptean-ross
kind: erp-summary
description: >-
  Use it when standing up or reading an Aptean Ross → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Aptean Ross: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/aptean-ross` (or `list_skills`) against the
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created and existing ones updated; none are deleted. | Account, Commercient Customers Managed Custom Object, Commercient Customer Addresses Managed Custom Object, Commercient Salespeople Managed Custom Object | customers, customer addresses, salespeople |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Sales Order Invoices Managed Custom Object, Commercient Sales Order Invoice Lines Managed Custom Object | sales order lines, product master records, sales order history records, sales order invoice lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Sales Order Headers Managed Custom Object, Commercient Sales Order Lines Managed Custom Object, Commercient Sales Order History Managed Custom Object, Commercient Sales Order Line Quantities Managed Custom Object | sales order headers, sales order lines, product master records, sales order history records, sales order line quantities |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Aptean Ross Salesperson master | Commercient Salespeople Managed Custom Object | Commercient external key (Aptean package) | 1 |
| Account | Account | Commercient AR customer code | 2 |
| Aptean Ross Customer master | Commercient Customers Managed Custom Object | Commercient external key (Aptean package) | 3 |
| Aptean Ross Customer address | Commercient Customer Addresses Managed Custom Object | Commercient external key (Aptean package) | 4 |
| Aptean Ross Sales order header | Commercient Sales Order Headers Managed Custom Object | Commercient external key (Aptean package) | 5 |
| Aptean Ross Sales order line | Commercient Sales Order Lines Managed Custom Object | Commercient external key (Aptean package) | 6 |
| Aptean Ross Sales order invoice | Commercient Sales Order Invoices Managed Custom Object | Commercient external key (Aptean package) | 7 |
| Aptean Ross Sales order invoice line | Commercient Sales Order Invoice Lines Managed Custom Object | Commercient external key (Aptean package) | 8 |
| Aptean Ross Sales order history | Commercient Sales Order History Managed Custom Object | Commercient external key (Aptean package) | 9 |
| Aptean Ross Sales order line quantity | Commercient Sales Order Line Quantities Managed Custom Object | Commercient external key (Aptean package) | 10 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | salespeople |
| account feed | — | customers, customer addresses |
| customer feed | insert + update | customers |
| customer address feed | insert + update | customer addresses |
| sales order header feed | insert + update | sales order headers |
| sales order line feed | insert + update | sales order lines, product master records, sales order history records |
| sales order line feed | insert + update | sales order lines, product master records, sales order history records |
| sales order invoice line feed | insert + update | sales order invoice lines |
| sales order history feed | insert + update | sales order history records |
| sales order line quantity feed | insert + update | sales order line quantities, sales order lines |

## 4. Order of work

The templates set run sequence from 1 to 10. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Aptean Ross Salesperson master
- 2 — Account
- 3 — Aptean Ross Customer master
- 4 — Aptean Ross Customer address
- 5 — Aptean Ross Sales order header
- 6 — Aptean Ross Sales order line
- 7 — Aptean Ross Sales order invoice
- 8 — Aptean Ross Sales order invoice line
- 9 — Aptean Ross Sales order history
- 10 — Aptean Ross Sales order line quantity

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads Salesperson master
- customer feed reads Account: Salesperson master
- customer address feed reads Account: Customer master, Salesperson master
- sales order line feed reads sales order header sync output, sales order line sync output
- sales order invoice line feed reads sales order invoice sync output
- sales order header feed reads Account: Customer master
- sales order line feed reads sales order header sync output
- sales order history feed reads Account: Customer master, sales order header sync output
- sales order line quantity feed reads sales order line sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Aptean Ross Salesperson master | Commercient Salespeople Managed Custom Object | 25 | Company code,Salesperson code → Commercient external key (Aptean package), Code description → Name, Record key → Record key, Company code → Company code, Code description → Code description |
| Account | Account | 15 | Company code, Division, Customer number → Commercient AR customer code, Customer name → Name, Phone → Phone, fax number property → Fax, System address line 1, System address line 2, Address line 3, Address line 4 → Billing street |
| Aptean Ross Customer master | Commercient Customers Managed Custom Object | 15 | Company code,Division,Customer number → Commercient external key (Aptean package), Customer name → Name, System address line 1,System address line 2,Address line 3,Address line 4 → Billing street, System address city → Billing city, State → Billing state |
| Aptean Ross Customer address | Commercient Customer Addresses Managed Custom Object | 103 | Company code,Division,Customer number,Address code → Commercient external key (Aptean package), Customer name → Commercient name, Record key → Commercient record key, Company code → Commercient company code, Division → Commercient division |
| Aptean Ross Sales order header | Commercient Sales Order Headers Managed Custom Object | 100 | Company code,Division,Order number → Commercient external key (Aptean package), Order number → Name, Record key → Record key, Company code → Company code, Division → Division |
| Aptean Ross Sales order line | Commercient Sales Order Lines Managed Custom Object | 107 | Record key → Commercient external key (Aptean package), Order number → Commercient order number, Order line number → Commercient order line number, Order line type → Commercient order line type, Product group → Commercient product group |
| Aptean Ross Sales order invoice | Commercient Sales Order Invoices Managed Custom Object | 109 | Record key → Commercient external key (Aptean package), Order number, Order line number → Commercient name, Company code → Commercient company code, Division → Commercient division, Order number → Commercient order number |
| Aptean Ross Sales order invoice line | Commercient Sales Order Invoice Lines Managed Custom Object | 123 | Company code, Division, Invoice number, Invoice line number → Commercient external key (Aptean package), Invoice number, Invoice line number → Name, Record key → Record key, Company code → Company code, Division → Division |
| Aptean Ross Sales order history | Commercient Sales Order History Managed Custom Object | 42 | Record key → Commercient external key (Aptean package), Order number, Order line number → Commercient name, Company code → Commercient company code, Division → Commercient division, Order number → Commercient order number |
| Aptean Ross Sales order line quantity | Commercient Sales Order Line Quantities Managed Custom Object | 24 | Order number, Order line number, Unit of measure → Name, Record key → Commercient record key, Company code → Commercient company code, Division → Commercient division, Order number → Commercient order number |

## 6. Community templates

The catalogue carries 29 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 29
- Default operations: insert on 29, update on 29, delete on 29
- Marked as circular sync: 0
- Licence groups they span: 9
- Destination objects: Account, Order product, Price book entry, Commercient Sales Order Headers
  Managed Custom Object, Commercient Sales Order Invoice Lines Managed Custom Object, Product,
  Aptean Address (custom object), AR Invoice Detail (custom object), AR Invoice Header (custom
  object), Commercient Customer Addresses Managed Custom Object, Commercient Customers Managed
  Custom Object, Commercient Sales Order History Managed Custom Object, Commercient Sales Order
  Invoices Managed Custom Object, Commercient Sales Order Line Quantities Managed Custom Object,
  Commercient Sales Order Lines Managed Custom Object, Commercient Salespeople Managed Custom
  Object, Customer Part Code (custom object), Order, Price book object, User
- Object display names: Account, Aptean Ross Sales order header, Aptean Ross Sales order invoice
  line, Account customer lookup, Aptean Ross Customer address, Aptean Ross Sales order history,
  Aptean Ross Sales order invoice, Aptean Ross Sales order line, Aptean Ross Sales order line
  quantity, Aptean Ross Salesperson master, AR Invoice, AR Invoice Detail, 13 more and a further
  template
- Template groups: Account, Invoice, Product, Sales order, CRM Order and Line, Customer Multi Ship
  Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/aptean-ross`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Aptean Ross → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/aptean-ross`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
