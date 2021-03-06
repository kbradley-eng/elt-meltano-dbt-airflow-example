# elt-meltano-dbt-airflow-example
Example ELT pipeline using meltano, dbt, airflow, postgres db and metabase

# covid-elt
COVID-19 data via singer tap with Meltano, DBT and airflow.
## Meltano set-up
Get meltano:
`docker pull meltano/meltano:latest`
Init:
`docker run -v $(pwd):/meltano -w /meltano meltano/meltano init meltano`
`cd meltano`
Check what taps are available.
`docker run -v $(pwd):/meltano -w /meltano meltano/meltano discover extractors`
If your one is not availbale then runL
`docker run --interactive -v $(pwd):/meltano -w /meltano meltano/meltano add --custom extractor tap-covid-19`
Answer the questions. Hit enter to accept defaults for everything except the following:
capabilities: `catalog, discover, state`
settings: `api_token, user_agent, start_date`
List all properties
`docker run -v $(pwd):/meltano -w /meltano meltano/meltano config tap-covid-19 list`
Set properties
`docker run -v $(pwd):/meltano -w /meltano meltano/meltano config tap-covid-19 set api_token TAP_COVID_19_API_TOKEN`
`docker run -v $(pwd):/meltano -w /meltano meltano/meltano config tap-covid-19 set user_agent ojsquire@gmail.com`
`docker run -v $(pwd):/meltano -w /meltano meltano/meltano config tap-covid-19 set start_date 2021-04-01T00:00:00Z`
check set properly
`docker run -v $(pwd):/meltano -w /meltano meltano/meltano config tap-covid-19`
check available streams
`docker run -v $(pwd):/meltano -w /meltano meltano/meltano select tap-covid-19 --list --all`
select the one(s) you want
`docker run -v $(pwd):/meltano -w /meltano meltano/meltano select tap-covid-19 eu_daily '*'`
set replicate type
`docker run -v $(pwd):/meltano -w /meltano meltano/meltano config tap-covid-19 set _metadata eu_daily replication-method INCREMENTAL`
set replication key if it's incremental (other types don't need one...)
`docker run -v $(pwd):/meltano -w /meltano meltano/meltano config tap-covid-19 set _metadata eu_daily replication-key __sdc_row_number`
### Add a loader
We are using a postgres db so:
`docker run -v $(pwd):/meltano -w /meltano meltano/meltano add loader target-postgres`
Set db settings:
`docker run -v $(pwd):/meltano -w /meltano meltano/meltano config target-postgres set postgres_port 5432`
`docker run -v $(pwd):/meltano -w /meltano meltano/meltano config target-postgres set postgres_username admin`
`docker run -v $(pwd):/meltano -w /meltano meltano/meltano config target-postgres set postgres_password meltano`
`docker run -v $(pwd):/meltano -w /meltano meltano/meltano config target-postgres set postgres_database dev`
`docker run -v $(pwd):/meltano -w /meltano meltano/meltano config target-postgres set postgres_schema covid`
Check everything is the way it should look:
`docker run -v $(pwd):/meltano -w /meltano meltano/meltano config target-postgres`




# now add the metlano production docker-compose  and dockerfiles.

`docker run -v $(pwd):/meltano -w /meltano meltano/meltano add files docker-compose`

`docker run -v $(pwd):/meltano -w /meltano meltano/meltano add files docker`

Now run:

docker-compose --env-file .env  up -d --build

If you go to localhost:5000 you shall see the meltano ui has been built.

We now need to spin up a postgres db for testing locally before we run an extraction.
