---
applyTo: "**/*.yaml,**/*.yml,**/templates/**,**/Chart.yaml"
description: "YAML and Helm chart conventions for Kubernetes manifests and configuration."
---

# YAML & Helm Chart Conventions

1. **Indentation** — 2 spaces, no tabs. This is enforced across all YAML files.
2. **Helm values** — Use `camelCase` for values keys. Use `{{ .Values.key }}` notation consistently.
3. **Chart structure** — Addon charts live in `otelcollector/deploy/addon-chart/`, standalone chart in `otelcollector/deploy/chart/prometheus-collector/`.
4. **Template comments** — Use `{{/* comment */}}` for Helm template comments. Add comments for non-obvious conditional logic.
5. **ConfigMap naming** — Follow existing naming patterns in `otelcollector/configmaps/`. Use descriptive names that indicate the scrape target or config purpose.
6. **Multi-platform** — Ensure Helm charts support both Linux and Windows node selectors where applicable. Check `nodeSelector` and `tolerations` settings.
7. **Default scrape configs** — Default Prometheus scrape configurations live in `otelcollector/configmapparser/default-prom-configs/`. Follow the existing YAML structure when adding new targets.
8. **Label management** — Be careful with high-cardinality labels in scrape configs. Use `metric_relabel_configs` to drop unnecessary labels.
