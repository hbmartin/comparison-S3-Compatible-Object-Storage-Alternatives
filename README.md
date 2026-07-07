# Comparison of S3-Compatible Object Storage Alternatives

| **Provider**               | **Free Tier Limitations**                                | **First Paid Tier / Base Cost**                       | **Storage Cost ($/GB/mo)**              | **Egress Cost ($/GB)**                        | **Minimum Retention & Key Limitations**                      |
| -------------------------- | -------------------------------------------------------- | ----------------------------------------------------- | --------------------------------------- | --------------------------------------------- | ------------------------------------------------------------ |
| **AWS S3**                 | 5 GB storage (12-month limit for new accounts)           | Pay-as-you-go; no base fee                            | $0.023 (first 50 TB)                    | $0.09 (first 100 GB free)                     | 30 to 180 days on colder tiers. Highly complex billing; IAM overhead. |
| **Azure Blob Storage**     | 5 GB, 15 GB egress (12-month limit)                      | Pay-as-you-go; no base fee                            | $0.0184 (Hot, LRS)                      | $0.087 (Premium)                              | 128 KiB min object size. 30/90/180 day retention on Cool/Cold/Archive. |
| **Google Cloud Storage**   | 5 GB storage, 100 GiB egress (NA only), 5k A / 50k B ops | Pay-as-you-go; no base fee                            | $0.020 (Standard, Regional)             | $0.08 - $0.12 (Highest of big 3)              | 30/90/365 day retention on Nearline/Coldline/Archive. Interop layer has S3 gaps. |
| **Oracle OCI**             | 20 GB Standard, 20 GB Archive, 10 TB global egress       | Pay-as-you-go; no base fee                            | $0.0255 (Standard)                      | $0.0085 (First 10 TB/mo free)                 | 31 days (IA), 90 days (Archive). Highly generous free egress allowance. |
| **Cloudflare R2**          | 10 GB storage, 1M Class A, 10M Class B ops/month         | Pay-as-you-go; no base fee                            | $0.015 (Standard)                       | **$0.00** (Unconditionally free)              | **No Versioning, No Object Lock.** Not WORM compliant. Class B reads dominate static hosting costs. |
| **Tigris**                 | 5 GB storage, 10k Class A, 100k Class B ops/month        | Pay-as-you-go; no base fee                            | $0.020 (Standard)                       | **$0.00** (Unconditionally free)              | 30/90 days on IA/Archive. Supports Object Lock/Versioning, dynamically placed globally. |
| **Backblaze B2**           | 10 GB storage (Always Free)                              | Pay-as-you-go ($6.95/TB/mo) or B2 Reserve ($1,560/yr) | $0.00695 ($6.95/TB)                     | $0.01 (Free up to 3x monthly storage)         | Only 3 global regions. 3x egress cap can be exceeded by heavy streaming. Free API calls. |
| **Wasabi**                 | 30-day trial (1 TB) only. No permanent free tier         | $7.99/mo (1 TB minimum billing floor)                 | $0.00799 ($7.99/TB)                     | **$0.00** (Subject to strict 1:1 ratio limit) | **90-day minimum retention per object.** Highly punitive for short-lived, high-churn data. |
| **IDrive e2**              | 10 GB storage (Always Free)                              | $2.95/mo (100 GB) or ~$5/TB base                      | $0.004 - $0.005 (~$4-5/TB)              | **$0.00** (Fair use / up to 3x limit)         | No versioning, No Object Lock. Zero minimum retention periods. |
| **DigitalOcean Spaces**    | None                                                     | $5.00/month                                           | $0.020 (after 250 GiB included)         | $0.01 (after 1 TiB included)                  | **5 GB maximum object size.** Fails on large database/video multipart uploads. |
| **Vultr Object Storage**   | None                                                     | $18.00/month (Standard Tier)                          | $0.018 (after 1,000 GB included)        | $0.01 (after 1,000 GB included)               | **5 GB maximum object size.** No SSE support; durability not publicly verifiable. |
| **Akamai / Linode**        | None                                                     | $5.00/month                                           | $0.020 (after 250 GB included)          | $0.005 (transfer pool)                        | **5 GB maximum object size.** No multipart support above 5 GB. |
| **Hetzner Object Storage** | None                                                     | €6.49/month                                           | €0.0087 / TB-hour (after 1 TB included) | €1.00 / TB (after 1 TB included)              | EU-only regions. Prices updated April 2026 due to hardware costs. Free API calls. |
| **Scaleway**               | 75 GB storage, 75 GB egress (Always Free)                | Pay-as-you-go; no base fee                            | €0.016 (Multi-AZ) / €0.008 (One Zone)   | €0.01 (after 75 GB free)                      | EU-only regions. Sovereign cloud protection against CLOUD Act. |
| **OVHcloud**               | Varies by plan                                           | Pay-as-you-go; no base fee                            | €0.007 - €0.01 (Standard)               | **$0.00** (Egress free as of Jan 2026)        | **30-day minimum retention on all tiers.** SecNumCloud qualified options. |
| **DanubeData**             | €50 signup credit                                        | €3.99/month                                           | €0.0039 (after 1 TB included)           | €0.0019 (after 1 TB included)                 | EU sovereign entity, no US CLOUD Act exposure. Unmetered requests. |



## The Architectures of Vendor Lock-In: Egress and the Escape Cost

When evaluating object storage alternatives, comparing the baseline per-gigabyte storage cost is often the least consequential metric for high-volume, dynamic workloads. The true financial burden of cloud storage is dictated by data transfer out (egress) to the internet or other cloud networks. The cost differential between network ingress, which is universally free across virtually all providers to eliminate onboarding friction, and network egress, which can range from free to over $0.12 per gigabyte, represents the largest structural billing asymmetry in modern cloud infrastructure.

This asymmetry creates a powerful financial lock-in mechanism often referred to as the "escape cost." The financial penalty for migrating data away from a hyperscaler fundamentally alters multi-cloud flexibility and disaster recovery strategies.

| **Provider**             | **Outbound Transfer Cost (per GB)** | **Cost to Migrate 100 TB Out** | **Free Egress Allowance**     |
| ------------------------ | ----------------------------------- | ------------------------------ | ----------------------------- |
| **AWS S3**               | $0.090 (first 10 TB)                | ~$9,000                        | 100 GB / month                |
| **Google Cloud Storage** | $0.120 (Standard Internet)          | ~$12,000                       | 100 GiB / month (NA only)     |
| **Azure Blob Storage**   | $0.087 (first 50 TB)                | ~$8,700                        | 100 GB / month                |
| **Cloudflare R2**        | $0.000                              | $0                             | Unlimited                     |
| **Backblaze B2**         | $0.010 (after allowance)            | ~$1,000 (if unmitigated)       | 3x average monthly storage    |
| **Wasabi**               | $0.000 (conditional)                | $0 (if within 1:1 limit)       | Equal to active stored volume |
| **Oracle OCI**           | $0.0085 (after allowance)           | ~$765                          | 10 TB / month                 |



