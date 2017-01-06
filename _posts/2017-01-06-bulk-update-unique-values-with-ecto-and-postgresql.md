---
layout: post
published: true
title: Bulk update unique values with Ecto and PostgreSQL
date: 2017-01-06 02:27:00
categories:
- web-development
tags:
- elixir
- phoenix
---

I recently needed to retrieve the latest log for a specific device in a Phoenix app. Simple, use DISTINCT ON right?

---


Nope! DISTINCT ON should be used carefully as it works great on an empty database but will have horrible performance when you get a large number of records in the table.

#### The Problem

In my case I was saving streaming logs to a table that could be searched and analyzed later. However I needed to frequently fetch the latest log for a set of devices. While bulk insert allows me to batch streaming logs together and then insert them many at a time how can I query the table to get the last log for each device? My naive version looked like this:

``` elixir
Repo.all(from l in Logs,
  where: l.device_id in ^ids,
  distinct: l.device_id,
  order_by: [desc: l.updated_at])
```

This is simple and easily returns the latest log for the specified devices. However it performs horribly, especially on data such as logs that may have millions of rows. Query times of 10+ seconds are possible with half a million rows.

In my case we didn't have to have the *very* last log available to query so batching them together for a minute or two and doing a bulk update was a possibility. I ended up adding a foreign key to the device table itself that could be updated to point to a specific log entry.

Using `Repo.insert_all/3` with `on_conflict: :replace_all` isn't possible because I only have the device ID at the time I'm inserting the log and I definitely don't want to query devices every time I insert logs. Neither could I use `Repo.update_all/2` as it is meant to update many rows to the same value, what I need is to update many rows with their own values.

#### The Solution

Luckily there's a way to do this using Postgres and Ecto allows us to run raw SQL queries for complex use cases such as this.

Assuming we have two tables that look like this:

``` elixir
defmodule MyApp.Device do
  use MyApp.Web, :model

  schema "devices" do
    field :name, :string
    belongs_to :last_log, MyApp.Log
  end
end

defmodule MyApp.Log do
  use MyApp.Web, :model

  schema "logs" do
    field :data, :string
    field :time, Timex.Ecto.DateTime
  end
end
```

We can insert the data as shown below. Note that since this is a raw query you'll need to coerce `device_ids` and `log_ids` to whatever `Postgrex` expects. Likely integer but may be `Ecto.UUID` if you use uuids for your keys.

``` elixir
# not shown: use Repo.insert_all to save logs, then deduplicate them so we have the latest log for each device

device_ids = [1, 2, 3]
log_ids = [5000, 6001, 6003]

sql = """
UPDATE devices
SET last_log_id = tmp.last_log_id
FROM
  (SELECT unnest($1::integer[]) AS id, unnest($2::integer[]) AS last_log_id) AS tmp
WHERE devices.id = tmp.id
"""

Ecto.Adapters.SQL.query(Repo, sql, [device_ids, log_ids])
```

Now reading the data is simple and super fast: whenever we fetch the device records we can select logs by their primary key. Storing the data is fast too, a few milliseconds to update a batch of devices to point to their latest log.

