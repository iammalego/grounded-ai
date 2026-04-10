# Changelog

All notable changes to the Grounded AI skill.

## [2.0.0] - 2025-04-10

### Major Release - "Smart Explorer"

Complete transformation from simple "read first" rule to intelligent exploration system with memory, classification, and validation.

#### Added

- **Memory Layer (Phase 1)**
  - File structure caching with engram persistence
  - SHA-256 content hashing for change detection
  - Dependency graph construction via AST analysis
  - Incremental update algorithm (O(k) vs O(n))

- **Query Classification (Phase 2)**
  - 6 intent types: deep-explore, quick-lookup, error-trace, pattern-compare, implementation, refactor
  - Pattern detection engine with 30+ regex patterns
  - Entity extraction (files, functions, classes, errors)
  - Confidence scoring: 40% pattern + 30% keywords + 30% entities
  - Dynamic entry point selection based on intent

- **Progressive Disclosure (Phase 3)**
  - 4-wave reading system:
    - Wave 1: Metadata (~50 tokens)
    - Wave 2: Signatures (~200 tokens)
    - Wave 3: Full body (~1000+ tokens)
    - Wave 4: Dependencies
  - File relevance scoring (TF-IDF + PageRank centrality)
  - Token budget management with adaptive depth
  - Wave planning by intent classification

- **Change Detection (Phase 4)**
  - SHA-256 hash comparison
  - Change type classification: added, modified, deleted, unchanged, moved
  - Cache invalidation strategy with TTL policies
  - Smart update decisions (partial vs full rebuild)

- **Validation System (Phase 5)**
  - Cross-reference checking (imports, symbols, types)
  - Multi-source corroboration
  - 5-factor confidence scoring:
    - Coverage (30%)
    - Query Match (25%)
    - Freshness (20%)
    - Validation (15%)
    - Pattern Match (10%)
  - Threshold-based response strategy

- **Session Bridge (Phase 6)**
  - Session state persistence
  - Pattern learning from exploration history
  - Query-to-files pattern mapping
  - Session resume capability
  - Multi-session exploration support

#### Performance Improvements

- Token reduction: 60-90% (was ~15,000 tokens, now ~200-2,000)
- Cold start: < 2 seconds
- Warm start: < 200 milliseconds
- Cache hit rate: > 80%

#### Documentation

- Complete rewrite of SKILL.md (~8,500 lines)
- Architecture documentation
- Real-world examples
- Performance benchmarks
- Research background

## [1.0.0] - 2025-04-10

### Initial Release - "Read Before Speaking"

Basic skill that forces AI to read codebase before responding.

#### Added

- Core rule: READ BEFORE SPEAKING
- File reading priority (README → package.json → entry points)
- Basic engram awareness
- Anti-patterns documentation
- Success criteria checklist

---

## Versioning

We use [Semantic Versioning](https://semver.org/):

- **MAJOR**: New exploration phase or architectural change
- **MINOR**: New patterns, improved algorithms, additional language support
- **PATCH**: Documentation fixes, typo corrections, minor refinements
