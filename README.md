# hsbcadculator

## Run locally

From the project root:

```bash
python3 -m http.server 8000
```

Open: http://localhost:8000

## Connect to Supabase

The app now includes Save/Load actions in the side menu and uses Supabase from the browser.

### 1) Create the table in Supabase (columns per input/output)

Run this in the Supabase SQL editor:

```sql
drop table if exists public.roi_scenarios;

create table public.roi_scenarios (
	id bigint generated always as identity primary key,
	name text not null,

	input_company_name text,
	input_company_revenue numeric,
	input_projects_per_year integer,
	input_elements_per_project integer,
	input_manual_stations integer,
	input_cnc_machines integer,
	input_product_types text[] default '{}',
	input_element_types text[] default '{}',
	input_other_element boolean default false,
	input_other_element_label text,
	input_num_designers integer,
	input_designer_salary numeric,
	input_num_supervisors integer,
	input_supervisor_salary numeric,
	input_num_operators integer,
	input_operator_salary numeric,
	input_cost_of_filing_time_per_hour numeric,
	input_filing_time_hours numeric,
	input_prints_per_project integer,
	input_cost_per_print numeric,
	input_error_rate numeric,
	input_cost_of_errors numeric,
	input_cost_hardware numeric,
	input_cost_current_server numeric,
	input_go_live_date text,
	input_country text,
	input_language text,

	setup_servers integer,
	setup_offices integer,
	setup_clients integer,

	output_roi_3_year numeric,
	output_net_benefit_year_3 numeric,
	output_payback_period text,
	output_average_annual_savings numeric,
	output_cumulative_benefit_3y numeric,
	output_products_total numeric,
	output_year1_total_cost numeric,
	output_year2_total_cost numeric,
	output_year3_total_cost numeric,
	output_cumulative_cost_3y numeric,

	state_current_input_step integer default 0,
	state_active_view text default 'input',
	state_map_zoom numeric default 1,
	state_map_node_state jsonb default '[]'::jsonb,
	state_manual_map_links jsonb default '[]'::jsonb,
	state_selected_map_node_id text,
	state_preview_connection_point jsonb,

	created_at timestamptz not null default now()
);
```

### 2) Add policies (simple demo setup)

If you want quick testing without auth, you can allow anon reads/writes:

```sql
alter table public.roi_scenarios enable row level security;

drop policy if exists "anon can insert scenarios" on public.roi_scenarios;
drop policy if exists "anon can read scenarios" on public.roi_scenarios;

create policy "anon can insert scenarios"
on public.roi_scenarios
for insert
to anon
with check (true);

create policy "anon can read scenarios"
on public.roi_scenarios
for select
to anon
using (true);
```

For production, replace this with authenticated policies.

### 3) Use Save/Load in the app

1. Click Save (or Load) in the side menu.
2. Enter your Supabase project URL and anon key (saved in localStorage).
3. Save returns a numeric scenario ID.
4. Use that ID in Load to restore a scenario.