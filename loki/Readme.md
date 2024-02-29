# Instal memcache
helm install chunk-cache bitnami/memcached -n loki
helm install result-cache bitnami/memcached -n loki
helm install index-cache bitnami/memcached -n loki

# Get svc
k get svc -n loki| grep memca

# Add to loki-config
...
loki:
  ...
  memcached:
    chunk_cache:
      enabled: true
      host: chunk-cache-memcached.dev-monitoring-ns.svc
      service: memcache
      batch_size: 256
      parallelism: 10
    results_cache:
      enabled: true
      host: results-cache-memcached.dev-monitoring-ns.svc
      service: memcache
      default_validity: 12h
...
boltdb_shipper:
        active_index_directory: /var/loki/botldb-index
        shared_store: s3
        cache_location: /var/loki/boltdb-cache
      index_queries_cache_config:
        memcached:
            batch_size: 256
            parallelism: 10
        memcached_client:
            host: index-cache-memcached.dev-monitoring-ns.svc
            service: memcache