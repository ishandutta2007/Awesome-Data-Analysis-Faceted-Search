# Awesome-Data-Analysis-Faceted-Search

# Top Data Analysis & Faceted Search Ecosystem

**Curated List of SaaS Products & Open-Source GitHub Projects**  
*Focused on Faceted Navigation, Aggregation Engines & Self-Hosted Search Analytics*  
**Last updated: March 2026**

This repository tracks notable **SaaS platforms** and **open-source projects** for **Data Analysis & Faceted Search**. These tools help users explore large datasets by narrowing results through dynamically generated filters—covering everything from commercial search APIs with built-in faceting to open-source engines that compute aggregation buckets and facet counts on your own infrastructure.

**Examples** include Elasticsearch, Algolia, Meilisearch, Typesense, OpenSearch, Vespa, Coveo, Sinequa, Lucidworks Fusion, and Mindbreeze Inspire (the category leaders).

**Open-source emphasis**: This section highlights every major active project for self-hosted faceted search, open-source aggregation engines, and community-driven search analytics—ideal for teams seeking data sovereignty and avoiding per-record or per-query SaaS pricing that compounds as datasets grow. The open-source landscape is genuinely mature: **Elasticsearch** implements faceted search via aggregations [citation:1][citation:13], **Meilisearch** provides granular control over facet performance [citation:3], and **Vespa** offers a declarative grouping language for computing facet buckets and statistics [citation:6].

Contributions welcome! Open an PR to add/update entries. Keep descriptions factual and link to official sites.

## Table of Contents
- [SaaS/Hosted Platforms](#saas-products)
- [Open-Source GitHub Projects](#open-source-github-projects)
- [How to Contribute](#how-to-contribute)
- [Disclaimer](#disclaimer)

## SaaS/Hosted Platforms

- **[Algolia](https://www.algolia.com/)**  
  **Polished search-as-a-service with explicit faceting controls.** Declare attributes for faceting via `attributesForFaceting`, then retrieve facet values and match counts with the `facets` parameter [citation:2][citation:14]. **Modifiers**: `filterOnly` for attributes used only in filters (reduces index size), `searchable` for type-ahead within facet values, and `afterDistinct` for accurate counts when deduplicating records [citation:2]. **Response includes `exhaustiveFacetsCount`**—true if counts are exact, false if approximate [citation:14]. **Pricing metered on records stored and search requests separately**.

- **[Elasticsearch Cloud](https://www.elastic.co/)**  
  **The reference implementation for faceted search via aggregations.** The `terms` aggregation creates buckets per unique keyword value with `doc_count`, powering sidebar facets [citation:1][citation:13]. **Date histogram** aggregation handles temporal facets for date fields [citation:1]. **Critical caveat**: on multi-shard indexes, `terms` aggregation counts can be **approximate**—the response includes `doc_count_error_upper_bound` (margin of error) and `sum_other_doc_count`; raising `shard_size` (e.g., to 200) improves accuracy at compute cost [citation:5].

- **[AWS OpenSearch](https://aws.amazon.com/opensearch-service/)**  
  **Managed fork of Elasticsearch 7.10.2 with the same aggregation-based faceting model.** **Terms aggregation on keyword fields** (not analyzed text) produces facet buckets; check `doc_count_error_upper_bound` for accuracy on sharded indexes [citation:5]. **`shard_size` controls candidate terms per shard**—default is `size * 1.5 + 10`, raise it when counts must match a source of truth [citation:5].

- **[Coveo](https://www.coveo.com/)**  
  **Enterprise search with granular facet configuration.** `FacetOptions` interface exposes `allowedValues` (explicit facet value allowlist, max 25), `customSort` (pinned values), `filterFacetCount` (exclude parent counts for folded results), `numberOfValues`, and `sortCriteria` [citation:7]. Positioned for teams wanting fine-grained control over facet presentation and behavior.

## Open-Source GitHub Projects

- **[Elasticsearch](https://github.com/elastic/elasticsearch)**  
  **The most widely deployed open-source faceted search engine.** **Apache 2.0 / ELv2** (OpenSearch is the Apache-2.0 fork). **Terms aggregation** is the canonical approach for keyword facets: `{"aggs": {"category-agg": {"terms": {"field": "category.keyword"}}}}` returns buckets with `key` and `doc_count` [citation:1][citation:13]. **Date histogram** handles year/month facets on date fields [citation:1]. **Multi-shard accuracy**: the coordinating node merges top terms from each shard; terms outside a shard's top list are missed, producing low counts—`shard_size` is the lever for exactness [citation:5]. **Best for teams wanting the reference implementation** with the broadest ecosystem and proven aggregation semantics.

- **[Meilisearch](https://github.com/meilisearch/meilisearch)**  
  **The most performance-conscious open-source faceting engine.** **Granular `filterableAttributes`** let you enable only the features each attribute needs: `filter.equality` for categories/tags, `filter.comparison` for numeric ranges, `facetSearch` for type-ahead within facet values [citation:3]. **`maxValuesPerFacet`** (default 100) controls how many distinct values are computed—set it to your UI's display count to avoid wasted work [citation:3]. **Disable facet search globally or per attribute** to reduce indexing overhead [citation:3]. **Request only the facets you need** at search time; each listed facet computes counts across all matching documents [citation:3]. **Best for teams wanting explicit control over facet performance trade-offs** without commercial licensing.

- **[Vespa](https://github.com/vespa-engine/vespa)**  
  **The most expressive open-source grouping language for faceted analytics.** **Declarative YQL grouping**: `select * from product where title contains "laptop" | all(group(category) each(output(count())))` [citation:6]. **Multiple facets in one query** via chained `all(group(...) each(output(count())))` statements—computed in a single execution [citation:6]. **Aggregations beyond counts**: `sum(attribute(price))`, `avg(attribute(rating))`, `min`/`max` for price ranges [citation:6]. **Sample documents per bucket** via `each(max(3) each(output(summary())))` [citation:6]. **Best for teams needing rich statistical faceting** (sums, averages, ranges) rather than simple counts.

- **[Typesense](https://github.com/typesense/typesense)**  
  **Open-source search with facet counts via `facet_by` parameter.** Search results include `facet_counts` with counts per facet value [citation:4]. **`search_cutoff_ms`** can return results early but **facet computation is not bound by this cutoff**—facet counts complete even if hits return early [citation:4]. **Best for teams wanting a permissively-licensed (GPL-3.0) faceting engine** with a public cloud pricing catalog.

### Additional Strong Open-Source Options

- **[Sinequa](https://github.com/sinequa/sba-angular)** — **Enterprise search with a component-based facet module.** `BsFacetModule` provides `sq-facet-card` (frame) and `sq-facet-list`/`sq-facet-tree`/`sq-facet-ngx-chart`/`sq-facet-range`/`sq-facet-timeline` (content) [citation:8]. Facets are fed by server-side aggregations configured in the query web service—about 15 aggregations available in the training instance [citation:8]. **Options**: `showCount`, `searchable`, `allowExclude`, `allowOr`, `allowAnd`, `expandedLevel` for tree facets [citation:8]. **Best for Angular teams** wanting a structured facet UI with server-driven aggregations.

- **[Mindbreeze Inspire](https://help.mindbreeze.com/)** — **Enterprise search with a REST API exposing facets.** `facets` array includes `id` (metadata name), `name` (display name), and `entries` with `html` (textual description), `count` (estimated results), and `query_expr` (constraint expression) [citation:10]. **Hierarchical filters** supported via nested `entries` [citation:10]. **`available_facets`** lists filterable properties with localized names [citation:10]. **`sort_date_facets_by_value`** controls whether date facets sort by year/month or by result count [citation:10]. **Best for teams** wanting a documented, standards-based API for faceted navigation.

- **[Lucidworks Fusion](https://doc.lucidworks.com/)** — **Enterprise search built on Apache Solr with faceting support.** Documentation covers faceting under query basics [citation:9]. **Best for teams already on Solr** wanting a commercial control plane with faceting and analytics.

- **[ncoevoet/facet](https://github.com/ncoevoet/facet)** — **A local-first photo library tool, not a search engine.** The `facet` project scans photos, scores them with ML models, and builds a searchable database—but it is **not a faceted search engine for arbitrary data** [citation:11]. The repository name appears to be a category mismatch; no aggregation or faceting API is exposed.

**Frameworks for building custom systems**: Combine **Elasticsearch** or **OpenSearch** for proven aggregation-based faceting, **Meilisearch** when facet performance tuning matters, and **Vespa** for statistical groupings (sums, averages, ranges). Use **Sinequa's facet module** as an Angular reference for UI patterns.

## How to Contribute

1. Fork the repo.
2. Add/edit entries in `README.md` (follow existing format).
3. Include: name, link, 1–2 sentence description, and whether it's SaaS or open-source.
4. Submit PR with a short explanation.

Star the repo if you find it useful!

## Disclaimer

- This is a **community-curated** list — not exhaustive and not an endorsement.
- Faceted search platforms handle potentially sensitive data and query patterns; enforce access controls and validate facet counts before relying on them for business decisions.
- **Facet counts on sharded indexes can be wrong.** **Elasticsearch and OpenSearch** return `doc_count_error_upper_bound` and `sum_other_doc_count`—if the error bound is nonzero, your facet counts are approximate [citation:5]. Raise `shard_size` (e.g., to 200) when accuracy matters more than speed [citation:5]. **Meilisearch** adds overhead at indexing and search time for every filterable attribute—use granular `filterableAttributes` and lower `maxValuesPerFacet` to avoid wasted work [citation:3]. **Algolia** exposes `exhaustiveFacetsCount`—check it before treating counts as exact [citation:14]. **Vespa** is the most expressive for statistical faceting but has a steeper learning curve than simple terms aggregations [citation:6]. Validate whether you need simple counts (Elasticsearch, Meilisearch) or rich aggregations (Vespa) before committing.

---

**Made for search engineers, data analysts, and organizations building faceted navigation infrastructure.**  
Let's make data analysis and faceted search more open, performant, and count-accurate.
