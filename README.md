# sqlmon
sqlmon allows anyone who knows SQL, engineers, product managers, devops, systems administrators, analysts... to surface business insights in Slack channels and generate alerts via Pagerduty. In a nutshell, sqlmon is a service that runs user-defined SQL queries on a cron schedule.

As an example, a fledgling e-commerce company could use the following sqlmon configuration:

```yaml
queries:
  - new-international-order
    description: >
      An international order was made in the last hour
    query: >
      SELECT id, country_code
      FROM orders
      WHERE created_at >= (NOW() - 3600)
      AND country_code != "us"
    database_url: $ORDERS_DATABASE_URL
    cron: "0 * * * *"
  - daily-orders-tally
    description: >
      Compute daily active users for today
    query: >
      SELECT count(*)
      FROM orders
      WHERE DATE(created_at) = CURDATE()
    database_url: $ORDERS_DATABASE_URL
    cron: "0 0 * * *"
  - new-users
    description: >
      New user count in the last 10 minutes
    query: >
      SELECT count(*)
      FROM users
      WHERE created_at >= (NOW() - 600)
    database_url: $USERS_DATABASE_URL
    cron: "0 0 * * *"
  - large-order-fraud
    description: > 
      Orders that are possibly fraudulent
    query: >
      SELECT id, customer_id, amount, country
      FROM orders
      WHERE amount >= 500
      AND country != "us"
      AND created_at >= (NOW() - 60)
    database_url: $USERS_DATABASE_URL
    cron: "* * * * *"
    alert: 
      presence: true
```

