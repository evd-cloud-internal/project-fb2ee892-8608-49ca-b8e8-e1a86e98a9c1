---
name: Home
assetId: 52b19543-0611-4102-b1ef-cbc83d734195
type: page
---

# Sales & Marketing Dashboard

```sql leads
SELECT
  id,
  source,
  created_at,
  status
FROM postgres_public_leads
ORDER BY created_at
```
```sql offers
SELECT
  o.id,
  o.lead_id,
  o.value_eur,
  o.created_at,
  o.status
FROM postgres_public_offers o
ORDER BY o.created_at
```

```sql orders
SELECT
  o.id,
  o.offer_id,
  o.value_eur,
  o.created_at
FROM postgres_public_orders o
ORDER BY o.created_at
```

```sql funnel
SELECT 'Leads' as stage, count(*) as count, 1 as sort_order FROM postgres_public_leads
UNION ALL
SELECT 'Qualified' as stage, count(*) as count, 2 as sort_order FROM postgres_public_leads WHERE status = 'qualified' OR status = 'converted'
UNION ALL
SELECT 'Converted' as stage, count(*) as count, 3 as sort_order FROM postgres_public_leads WHERE status = 'converted'
UNION ALL
SELECT 'Offers Sent' as stage, count(*) as count, 4 as sort_order FROM postgres_public_offers
UNION ALL
SELECT 'Orders' as stage, count(*) as count, 5 as sort_order FROM postgres_public_orders
ORDER BY sort_order
```

{% big_value
  data="leads"
  value="count(id)"
  title="Total Leads"
/%}

{% big_value
  data="leads"
  value="count(id)"
  title="Converted Leads"
  where="status = 'converted'"
/%}

{% big_value
  data="orders"
  value="count(id)"
  title="Total Orders"
/%}

{% big_value
  data="orders"
  value="sum(value_eur)"
  title="Total Revenue"
  fmt="eur"
/%}

## Leads Over Time

{% line_chart
  data="leads"
  x="created_at"
  y="count(id)"
  date_grain="month"
  title="New Leads per Month"
/%}

## Leads by Source

{% bar_chart
  data="leads"
  x="source"
  y="count(id)"
  title="Leads by Source"
  order="count(id) desc"
/%}

## Lead Status Breakdown

{% bar_chart
  data="leads"
  x="status"
  y="count(id)"
  title="Lead Status Distribution"
  order="count(id) desc"
/%}

## Sales Funnel

{% bar_chart
  data="funnel"
  x="stage"
  y="count"
  title="Sales Funnel"
  x_sort="data"
/%}

## Recent Leads

{% table data="leads" /%}