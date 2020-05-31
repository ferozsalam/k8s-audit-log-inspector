

How to export to files the saved objects in Kibana


curl -X POST "localhost:5601/api/saved_objects/_export" -H 'kbn-xsrf: true' -H 'Content-Type: application/json' -d '
{
  "type": "index-pattern"
}' > index-patterns.ndjson


curl -X POST "localhost:5601/api/saved_objects/_export" -H 'kbn-xsrf: true' -H 'Content-Type: application/json' -d '
{
 "type": "visualization"
}' > visualizations.ndjson


curl -X POST "localhost:5601/api/saved_objects/_export" -H 'kbn-xsrf: true' -H 'Content-Type: application/json' -d '
{
  "type": "dashboard"
}' > dashboards.ndjson
