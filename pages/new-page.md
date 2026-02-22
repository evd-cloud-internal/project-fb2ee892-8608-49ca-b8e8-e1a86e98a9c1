---
name: New Page
assetId: cf22e5bb-665e-490c-904d-0f982b2496a3
type: page
---

# 📈 Sales Dashboard

```sql orders
SELECT
  id,
  value_eur,
  created_at
FROM postgres_public_orders
ORDER BY created_at
```

```sql converted_leads
SELECT
  id,
  source,
  created_at
FROM postgres_public_leads
WHERE status = 'converted'
ORDER BY created_at
```

```sql sales_by_source
SELECT
  l.source,
  count(DISTINCT l.id) as total_leads,
  count(DISTINCT CASE WHEN l.status = 'converted' THEN l.id END) as converted_leads,
  count(DISTINCT CASE WHEN l.status = 'converted' THEN l.id END) * 1.0 / count(DISTINCT l.id) as conversion_rate
FROM postgres_public_leads l
GROUP BY l.source
ORDER BY converted_leads DESC
```

```sql funnel
SELECT 'Leads' as stage, count(*) as count, 1 as sort_order FROM postgres_public_leads
UNION ALL
SELECT 'Qualified' as stage, count(*) as count, 2 as sort_order FROM postgres_public_leads WHERE status = 'qualified' OR status = 'converted'
UNION ALL
SELECT 'Converted' as stage, count(*) as count, 3 as sort_order FROM postgres_public_leads WHERE status = 'converted'
UNION ALL
SELECT 'Orders' as stage, count(*) as count, 4 as sort_order FROM postgres_public_orders
ORDER BY sort_order
```

{% big_value data="orders" value="sum(value_eur)" title="Gesamtumsatz" fmt="eur" /%}

{% big_value data="orders" value="count(id)" title="Bestellungen" /%}

{% big_value data="converted_leads" value="count(id)" title="Konvertierte Leads" /%}

{% big_value data="orders" value="avg(value_eur)" title="Ø Bestellwert" fmt="eur" /%}

## Sales Funnel

{% bar_chart
  data="funnel"
  x="stage"
  y="count"
  title="Von Leads zu Bestellungen"
  x_sort="data"
  data_labels={ position="above" }
/%}

## Conversion Rate nach Quelle

{% bar_chart
  data="sales_by_source"
  x="source"
  y="conversion_rate"
  y_fmt="pct1"
  title="Conversion Rate nach Quelle"
  order="conversion_rate desc"
  data_labels={ position="above" fmt="pct1" }
/%}

## Konvertierte Leads über Zeit

{% line_chart
  data="converted_leads"
  x="created_at"
  y="count(id)"
  date_grain="month"
  title="Monatliche Conversions"
/%}

## Leads nach Quelle

{% bar_chart
  data="converted_leads"
  x="source"
  y="count(id)"
  title="Konvertierte Leads nach Quelle"
  order="count(id) desc"
/%}

## Alle Bestellungen

{% table data="orders" /%}