---
layout: post
title: "Cloud security homelab notes"
date: 2025-12-20 09:00:00 +0500
categories: [Labs]
tags: [aws, terraform, iam, networking]
description: "Scratchpad from building an AWS sandbox with least-privilege IAM and network guardrails."
---

I have been tightening a small AWS lab to practice identity and network controls. A quick snapshot:

- **Landing zone:** Separate dev and lab accounts with IAM Identity Center and short-lived roles for admin and audit.
- **Networking:** VPC with public and private subnets, NAT gateway for egress, and network ACLs that mirror the security group rules for extra defense.
- **IAM hardening:** Service control policies to block wildcards, deny public S3 ACLs, and require MFA for console and role assumption.
- **IaC:** Terraform modules for VPC, SG baselines, and S3 buckets with encryption + block public access set to `true`.
- **Logging:** CloudTrail + S3 server access logs shipped to a central logging bucket with lifecycle rules.

Next steps are adding GuardDuty findings into a small Python Slack notifier and experimenting with automation for temporary elevated roles.
