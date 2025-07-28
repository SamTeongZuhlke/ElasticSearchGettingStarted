# ElasticSearch: Getting Started with Docker

## Starting local Elastic cluster using Docker Compose

Official documentation:

- <https://www.elastic.co/docs/deploy-manage/deploy/self-managed/install-elasticsearch-docker-compose>

To start elastic cluster (single-node):

```
docker compose -f ".\docker-compose.yaml" -p elastic-local up -d
```

To start elastic cluster (multi-node):

```
docker compose -f ".\docker-compose-multinode.yaml" -p elastic-local up -d
```

To get elastic cluster status:

```
docker compose -p elastic-local ps --format "table {{.Name}}\t{{.Image}}\t{{.Status}}"
```

To start elastic cluster and wait for it to be ready, for example:

```
if (docker compose -p elastic-local ps --format "{{.Name}}" | Select-String "es01") { $response = Read-Host "Cluster is already running. Do you want to restart it? (yes/no)"; if ($response.ToLower() -in @("yes", "y")) { docker compose -p elastic-local down; docker compose -f ".\docker-compose.yaml" -p elastic-local up -d;} elseif ($response.ToLower() -in @("no", "n")) { return; }} else { docker compose -f ".\docker-compose.yaml" -p elastic-local up -d; } do { $status = docker compose -p elastic-local ps --format "{{.Name}}:{{.Health}}"; $kibanaStatus = ($status | Select-String "^kibana:" | ForEach-Object { $_.ToString().Split(":")[1] }); Write-Host "`rWaiting for Kibana ($kibanaStatus) to be healthy" -NoNewline; $health = docker compose -p elastic-local ps --format "{{.Health}}"; Start-Sleep 1;} while ($health -match "^(?!healthy$)"); Write-Host "`rCluster is ready                                  "
```

To stop elastic cluster:

```
docker compose -p elastic-local down
```

To stop elastic cluster and remove volumes:

```
docker compose -p elastic-local down -v
```

Once elastic cluster is started, access Kibana UI via http://localhost:5601 and access Elastic via https://localhost:9200
