---
name: dlake-crmpro-salesforce/erps/infor-syteline-v7-and-v8
kind: erp-summary
description: >-
  Use it when standing up or reading an Infor SyteLine version 7 and version 8 → Salesforce template
  set, when deciding which templates to import and activate, or when a run completes without pushing
  records and the answer is in the view or the configuration row. It extends dlake-crmpro, which
  covers operating CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is
  a child of, which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Infor SyteLine version 7 and version 8: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-syteline-v7-and-v8` (or `list_skills`)
against the Commercient admin plane. Existing customers who need access or help: contact
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | account, Contact, Commercient Customer Managed Custom Object, Commercient Salesperson Managed Custom Object | customers, customer addresses, contacts, customer contacts, salespeople |
| **CRM Opportunity and Line** | ERP customer order header, customer order item data becomes Opportunity, Opportunity line item in Salesforce. New records are created and existing ones updated; none are deleted. | Opportunity, Opportunity line item | customer orders, customer order lines |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Customer Address Managed Custom Object | customer addresses (all sites) |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Invoice Header Managed Custom Object, Commercient Invoice Line Managed Custom Object | invoice headers, invoice lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Customer Order Managed Custom Object, Commercient Customer Order Line Managed Custom Object | customer orders, customer order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Get Users | User | Commercient salesperson code | 1 |
| Salesperson | Commercient Salesperson Managed Custom Object | Commercient external key (Infor package) | 1 |
| account | account | Commercient AR customer code | 2 |
| Customer | Commercient Customer Managed Custom Object | Commercient external key (Infor package) | 3 |
| Contact | Contact | External key (custom field) | 4 |
| Customer order | Commercient Customer Order Managed Custom Object | Commercient external key (Infor package) | 4 |
| Customer order line | Commercient Customer Order Line Managed Custom Object | Commercient external key (Infor package) | 5 |
| Invoice header | Commercient Invoice Header Managed Custom Object | Commercient external key (Infor package) | 6 |
| Invoice line item | Commercient Invoice Line Managed Custom Object | Commercient external key (Infor package) | 7 |
| Estimate | Opportunity | External key (custom field) | 8 |
| Estimate line | Opportunity line item | External key (custom field) | 9 |
| Customer Address | Commercient Customer Address Managed Custom Object | Commercient external key (Infor package) | 9 |

The active setting is inserted as 1 on Contact, Customer Address and as 0 on the rest, so some of
these processes start active on import.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | salespeople |
| CRM account feed | insert + update | customers, customer addresses |
| customer feed | insert only | customers, customer addresses |
| contact feed | insert + update | contacts, customer contacts |
| customer order feed | insert + update | customer orders |
| customer order line feed | insert + update | customer order lines |
| invoice feed | insert + update | invoice headers |
| invoice line feed | insert + update | invoice lines |
| estimate feed | insert + update | customer orders |
| estimate line feed | insert + update | customer order lines |
| customer address feed | insert + update | customer addresses (all sites) |

## 4. Order of work

The templates set run sequence from 1 to 9. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Get Users, Salesperson
- 2 — account
- 3 — Customer
- 4 — Contact, Customer order
- 5 — Customer order line
- 6 — Invoice header
- 7 — Invoice line item
- 8 — Estimate
- 9 — Estimate line, Customer Address

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- CRM account feed reads user sync output, salesperson sync output, Salesforce account sync output
  (generic name); no template in this set writes Salesforce account sync output (generic name)
- contact feed reads Salesforce account sync output
- estimate feed reads Salesforce account sync output (generic name), customer sync output (generic
  short name); no template in this set writes Salesforce account sync output (generic name),
  customer sync output (generic short name)
- estimate line feed reads estimate sync output (generic name)
- customer feed reads Salesforce account sync output, salesperson sync output
- customer address feed reads Salesforce account sync output
- invoice feed reads Salesforce account sync output, customer sync output, salesperson sync output
- invoice line feed reads invoice sync output
- customer order feed reads Salesforce account sync output, customer sync output, salesperson sync
  output
- customer order line feed reads customer order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Salesperson | Commercient Salesperson Managed Custom Object | 11 | Site reference,Salesperson code → external key column, Site reference → Site reference, Salesperson code → Salesperson code, outside → outside, Reference number → Reference number |
| account | account | 13 | Site reference, ERP customer number → Commercient AR customer code, city property → Billing city, state → Billing state, country → Billing country, city property → Shipping city |
| Customer | Commercient Customer Managed Custom Object | 93 | Site reference,ERP customer number → Commercient external key (Infor package), name → Name, ERP customer number → ERP customer number, Site reference → Site reference, Customer sequence number → Customer sequence number |
| Contact | Contact | 13 | Given name → Given name, Family name → Family name, Email → Email, Phone → Phone, Fax → Fax |
| Customer order | Commercient Customer Order Managed Custom Object | 4 | Site reference,Customer order number → external key column, Salesperson code → Commercient Infor SyteLine salesperson (related record), ERP customer number → the linked Infor SyteLine customer, Site reference,ERP customer number → Account |
| Customer order line | Commercient Customer Order Line Managed Custom Object | 3 | Site reference, Customer order number, Customer order line, Customer order release → Commercient external key (Infor package), the linked Salesforce record → Commercient customer order (related record), Site reference, Customer order number, Customer order line, Customer order release → Name |
| Invoice header | Commercient Invoice Header Managed Custom Object | 31 | Site reference,Invoice number → Commercient external key (Infor package), Site reference,Invoice number → Name, Site reference → Site reference, Invoice number → Invoice number, Invoice sequence → Invoice sequence |
| Invoice line item | Commercient Invoice Line Managed Custom Object | 21 | Site reference,Invoice number,Customer order line,Customer order release → Commercient external key (Infor package), Site reference,Invoice number,Customer order line,Customer order release → Commercient name, Site reference → Commercient site, Invoice number → Commercient invoice number, Invoice sequence → Commercient invoice sequence |
| Estimate | Opportunity | 6 | Customer order number → External key (custom field), Customer order number → Name, price → Amount, Order date → close date property, Stage → Stage |
| Estimate line | Opportunity line item | 7 | Customer order number, Customer order line, Customer order release → External key (custom field), item → Product code, Quantity ordered → Quantity, price → Unit price, Line discount → Discount |
| Customer Address | Commercient Customer Address Managed Custom Object | 43 | Account → Account, ERP customer number → Commercient customer number, Customer sequence number → Commercient customer sequence, city property → Commercient city, state → Commercient state |

## 6. Community templates

The catalogue carries 58 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 58
- Default operations: insert on 58, update on 58, delete on 58
- Marked as circular sync: 0
- Licence groups they span: 10
- Destination objects: Account, Commercient Customer Order Managed Custom Object, Commercient
  Customer Order Line Managed Custom Object, Commercient Customer Managed Custom Object, Commercient
  Invoice Header Managed Custom Object, Commercient Invoice Line Managed Custom Object, Commercient
  Salesperson Managed Custom Object, Contact, Opportunity, Opportunity line item, Product, account,
  Account contact relation, Commercient Customer Address Managed Custom Object, Commercient Item
  Managed Custom Object and 2 custom objects
- Object display names: Customer, Customer order, Customer order line, Estimate, Estimate line,
  Invoice header, Invoice line item, Salesperson, account, Account contact relation, Child Account
  and 9 more
- Template groups: Account, Product, CRM Opportunity and Line, Invoice, Sales order, Customer Multi
  Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-syteline-v7-and-v8`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Infor SyteLine version 7 and version 8 → Salesforce templates set up.
dlake-crmpro-salesforce is the destination skill this page sits under: its own text is the authority
for the Salesforce conventions that hold across every ERP, and its ERP table lists this page
alongside every sibling ERP page for this destination. For the extract leg that fills the source
data, see dlake-normalsync; for the on-premises agent that runs it, dlake-syncagent; for the
writeback leg, dlake-txdownloaderpro; for standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/infor-syteline-v7-and-v8`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
