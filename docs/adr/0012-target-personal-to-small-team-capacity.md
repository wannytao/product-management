# Target personal-to-small-team capacity without enterprise infrastructure

The application will remain a self-hosted modular monolith, but its schema, query boundaries, pagination, and indexes will be designed and tested against a per-Workspace envelope of approximately 20 Products, 25,000 Product Backlog Items, and 250,000 revision or snapshot records. Representative core reads and commands will target a P95 response time of no more than two seconds on a 4-vCPU, 8-GB host under the supported PostgreSQL configuration.

The design will also avoid assumptions that prevent approximately ten concurrent users in a later multi-user version, while V1 itself continues to support only one authenticated Workspace Owner. This headroom is a test and architecture constraint, not authorization to add enterprise infrastructure such as microservices, distributed caching, background workers, or high-availability clustering.
