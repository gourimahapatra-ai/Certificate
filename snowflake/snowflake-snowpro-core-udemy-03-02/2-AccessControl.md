### Account Structure
Snowflake object hierarchy (organization → account → database → schema → table/view) is officially documented in the Access Control Overview.
Snowflake defines securable objects inside a strict hierarchy:
Hierarchy:
Organization → Account → Database → Schema → Table/View

### RBAC (Role-Based Access Control)
Snowflake uses RBAC as the recommended model, with privileges assigned to roles and roles assigned to users

# 🎨 **Key System Roles (Colored Markdown Version)**

| Role | Purpose | Source |
|------|---------|---------|
| <span style="color:#ff9800; font-weight:bold;">ORGADMIN</span> | 🌐 Organization‑level admin; manages multi‑account operations. Rarely used for daily operations. | select.dev |
| <span style="color:#e91e63; font-weight:bold;">ACCOUNTADMIN</span> | 🏛️ Highest account‑level role; manages billing, configurations, and global parameters. Not for daily use. | docs.snowflake.com |
| <span style="color:#3f51b5; font-weight:bold;">SECURITYADMIN</span> | 🔐 Manages users, roles, and privilege grants. Child of ACCOUNTADMIN. | docs.snowflake.com |
| <span style="color:#009688; font-weight:bold;">USERADMIN</span> | 👤 Creates and manages users & roles; child of SECURITYADMIN. | docs.snowflake.com |
| <span style="color:#4caf50; font-weight:bold;">SYSADMIN</span> | 🏗️ Creates warehouses, databases, schemas, and database objects. | docs.snowflake.com |
| <span style="color:#9e9e9e; font-weight:bold;">PUBLIC</span> | 🔓 Default role with minimal privileges. | select.dev |


RBAC Concepts
Official Snowflake RBAC model includes:
- DAC — Owner can grant access.
- RBAC — Privileges → Roles → Users.
- UBAC — Direct user grants (not recommended).

Best Practices (SnowPro Exam Relevant)
Best Practices (SnowPro Exam Relevant)

Use ACCOUNTADMIN sparingly (break-glass role only).
All custom roles should roll up to SYSADMIN.
SECURITYADMIN handles granting; USERADMIN handles identity.
Always apply least privilege & future grants.

### Best practices reference:

### 2.4 Data Governance
2.4.1 Access Control: GRANT / REVOKE
RBAC best practices define how privileges are assigned to roles, not users. [docs.snowflake.com]
Examples from Snowflake documentation:
GRANT SELECT, USAGE, CREATE TABLE at various levels.

### Official GRANTS Docs:
https://docs.snowflake.com/en/sql-reference/sql/grant
https://docs.snowflake.com/en/sql-reference/sql/revoke

# Access Control Considerations — Summary (Snowflake)

This document outlines Snowflake's recommended practices and important considerations for securely configuring **Role‑Based Access Control (RBAC)** within an account. It explains how key system roles should be used and precautions for assignment, especially for highly privileged roles.

## 1. Overview of RBAC Best Practices

Snowflake recommends using **RBAC** as the primary access control mechanism.  
Privileges are granted to **roles**, and roles are granted to **users**.  
User-Based Access Control (UBAC) is supported but not recommended for most cases.  
[1](https://docs.snowflake.com/en/user-guide/security-access-control-considerations)

## 2. ACCOUNTADMIN Role (Critical Considerations)

### Purpose
- The **ACCOUNTADMIN** role is the *most powerful role* in Snowflake.  
- Responsible for configuring **account‑level parameters**, viewing/managing **billing and credits**, and stopping running queries.[1](https://docs.snowflake.com/en/user-guide/security-access-control-considerations)

### Important Notes
- ACCOUNTADMIN is **not** a superuser — it only has access to objects if its role or a lower role has privileges.  
- It sits at the top of the **system role hierarchy**.[1](https://docs.snowflake.com/en/user-guide/security-access-control-considerations)

### Recommended Precautions
Snowflake strongly recommends:

1. **Assign to very few users**  
2. **Require Multi‑Factor Authentication (MFA)** for all users with ACCOUNTADMIN  
3. **Ensure redundancy** → assign the role to *at least two users*  
4. **Do NOT use ACCOUNTADMIN for daily operations**  
   - Instead, business operations should use SYSADMIN or custom roles.  
5. **Do not create objects using ACCOUNTADMIN**  
   - Avoid complications where ACCOUNTADMIN owns the objects.[1](https://docs.snowflake.com/en/user-guide/security-access-control-considerations)


## 3. Default Role Assignment on New Accounts

- When a Snowflake account is first provisioned, the initial user receives the **ACCOUNTADMIN** role.  
- This user should immediately create additional users and assign them the **USERADMIN** role.[1](https://docs.snowflake.com/en/user-guide/security-access-control-considerations)

### User Creation Responsibilities
- All remaining user accounts should be created by:
  - **USERADMIN**, or  
  - A role with the **global CREATE USER** privilege[1](https://docs.snowflake.com/en/user-guide/security-access-control-considerations)

ACCOUNTADMIN
└── SECURITYADMIN
└── USERADMIN
└── SYSADMIN

### Role Responsibilities

| Role | Responsibilities |
|------|------------------|
| **ACCOUNTADMIN** | Account-level configuration, billing, security oversight |
| **SECURITYADMIN** | Global MANAGE GRANTS privilege; manages grants on all objects |
| **USERADMIN** | Creates and manages users and roles |
| **SYSADMIN** | Creates warehouses, databases, schemas, and all database objects |[1](https://docs.snowflake.com/en/user-guide/security-access-control-considerations)


## 5. USERADMIN Role Considerations

- USERADMIN can **create and manage users and roles**.  
- Ownership of a role or user may be transferred to other roles, limiting USERADMIN’s control.  
[1](https://docs.snowflake.com/en/user-guide/security-access-control-considerations)


## 6. SECURITYADMIN Role Considerations

- SECURITYADMIN has the **MANAGE GRANTS** privilege.  
- This allows granting or revoking privileges on objects across the account.  
- USERADMIN is a **child** of SECURITYADMIN in hierarchy.[1](https://docs.snowflake.com/en/user-guide/security-access-control-considerations)


## 7. SYSADMIN Role Considerations

- SYSADMIN creates **warehouses, databases, schemas, tables**, and other database objects.  
- Recommended for all day‑to‑day object management.[1](https://docs.snowflake.com/en/user-guide/security-access-control-considerations)

## 8. Best Practices Summary

### Always Use Least Privilege
Assign only the permissions necessary for a given role.

### MFA for High-Privilege Roles
Especially for:
- ACCOUNTADMIN
- SECURITYADMIN
- SYSADMIN[1](https://docs.snowflake.com/en/user-guide/security-access-control-considerations)

### Avoid ACCIDENTAL Use of ACCOUNTADMIN
- Never set ACCOUNTADMIN as a user’s **default role**.  
- Use lower‑privileged roles for normal operations.  
[1](https://docs.snowflake.com/en/user-guide/security-access-control-considerations)

### Multiple ACCOUNTADMIN Users
Having at least two ensures:
- Password recovery  
- Avoiding administrative lockout  
[1](https://docs.snowflake.com/en/user-guide/security-access-control-considerations)

# Final Notes
This Snowflake documentation emphasizes **strong governance**, **least privilege**, and **strict control** over highly privileged roles, particularly **ACCOUNTADMIN**. Proper assignment of SYSADMIN, SECURITYADMIN, and USERADMIN is necessary for secure and scalable account management.[1](https://docs.snowflake.com/en/user-guide/security-access-control-considerations)

## Key Concepts
Securable Objects
Entities to which access can be granted. Examples:


Databases -> Schemas -> Tables -> Views -> Functions -> Stages

Access is denied by default unless explicitly granted.

### Role
Collection of privileges.
Can be assigned to users or to other roles (forming a hierarchy). [docs.snowflake.com]

### Privilege

Specific level of access (e.g., SELECT, INSERT, CREATE, USAGE).
Controls how granular actions can be performed on securable objects.

### User
Identity recognized by Snowflake (human or service account).
Performs operations by activating roles.

### Role Hierarchy & Privilege Inheritance

Roles can be granted to other roles.
Parent roles inherit privileges of child roles.
Enables scalable, nested access control.

### 4. Object Hierarchy in Snowflake
Snowflake organizes securable objects inside logical containers:

Organization
└── Account
    └── Database
        └── Schema
            └── Table / View / Function / Stage
[1](https://docs.snowflake.com/en/en/user-guide/security-access-control-overview)


To *own* an object means the role holds the **OWNERSHIP** privilege on it.  
[1](https://docs.snowflake.com/en/en/user-guide/security-access-control-overview)

### Role Responsibilities

| Role | Responsibilities |
|------|------------------|
| **ACCOUNTADMIN** | Account-level configuration, billing, security oversight |
| **SECURITYADMIN** | Global MANAGE GRANTS privilege; manages grants on all objects |
| **USERADMIN** | Creates and manages users and roles |
| **SYSADMIN** | Creates warehouses, databases, schemas, and all database objects |[1](https://docs.snowflake.com/en/user-guide/security-access-control-considerations)

## 5. USERADMIN Role Considerations

- USERADMIN can **create and manage users and roles**.  
- Ownership of a role or user may be transferred to other roles, limiting USERADMIN’s control.  
[1](https://docs.snowflake.com/en/user-guide/security-access-control-considerations)


## 6. SECURITYADMIN Role Considerations

- SECURITYADMIN has the **MANAGE GRANTS** privilege.  
- This allows granting or revoking privileges on objects across the account.  
- USERADMIN is a **child** of SECURITYADMIN in hierarchy.[1](https://docs.snowflake.com/en/user-guide/security-access-control-considerations)


## 7. SYSADMIN Role Considerations

- SYSADMIN creates **warehouses, databases, schemas, tables**, and other database objects.  
- Recommended for all day‑to‑day object management.[1](https://docs.snowflake.com/en/user-guide/security-access-control-considerations)


## 8. Best Practices Summary

### Always Use Least Privilege
Assign only the permissions necessary for a given role.

### MFA for High-Privilege Roles
Especially for:
- ACCOUNTADMIN
- SECURITYADMIN
- SYSADMIN[1](https://docs.snowflake.com/en/user-guide/security-access-control-considerations)

### Avoid ACCIDENTAL Use of ACCOUNTADMIN
- Never set ACCOUNTADMIN as a user’s **default role**.  
- Use lower‑privileged roles for normal operations.  
[1](https://docs.snowflake.com/en/user-guide/security-access-control-considerations)

### Multiple ACCOUNTADMIN Users
Having at least two ensures:
- Password recovery  
- Avoiding administrative lockout  
[1](https://docs.snowflake.com/en/user-guide/security-access-control-considerations)

# Final Notes

This Snowflake documentation emphasizes **strong governance**, **least privilege**, and **strict control** over highly privileged roles, particularly **ACCOUNTADMIN**. Proper assignment of SYSADMIN, SECURITYADMIN, and USERADMIN is necessary for secure and scalable account management.[1](https://docs.snowflake.com/en/user-guide/security-access-control-considerations)


# Snowflake GRANT / REVOKE Privileges — Detailed Guide

This document provides a comprehensive overview of how **GRANT** and **REVOKE** privileges work in Snowflake, including supported privilege types, object scopes, syntax patterns, and best practices.

# 1. Overview

Snowflake access control is based on **RBAC** (Role-Based Access Control).  
Privileges are granted **to roles**, and roles are granted **to users**.  
Privileges can be **revoked** from roles or shares.  
[1](https://docs.snowflake.com/en/sql-reference/sql/revoke-privilege)

Snowflake supports privileges at multiple levels:
- Global (ACCOUNT)
- Account objects (warehouses, resource monitors, databases)
- Database-level privileges
- Schema-level privileges
- Object-level privileges (tables, views, stages, file formats, UDFs, sequences)
[1](https://docs.snowflake.com/en/sql-reference/sql/revoke-privilege)

# 2. GRANT Privileges

The `GRANT` command assigns privileges on securable objects to a **role**, **database role**, or **share**.

Snowflake supports granting:

### **2.1 Types of Grant Scopes**
- **Individual object grants** — e.g., `GRANT SELECT ON TABLE T`
- **Bulk ALL object grants** — e.g., `GRANT SELECT ON ALL TABLES IN SCHEMA`
- **Bulk FUTURE object grants** — e.g., `GRANT SELECT ON FUTURE TABLES IN SCHEMA`
- **Ownership transfer** via `GRANT OWNERSHIP` (cannot be revoked)
[2](https://docs.snowflake.com/en/sql-reference/functions/explain_grantable_privileges)[3](https://docs.snowflake.com/en/en/sql-reference/sql/grant-ownership)

### **2.2 Syntax (General Format)**

```sql
GRANT <privileges>
ON <object_type> <object_name>
TO ROLE <role_name>;

```

# 2.3 Common Privileges

## Object Type vs Privileges

| Object Type   | Privileges |
|---------------|------------|
| **Database**  | USAGE, CREATE SCHEMA, MONITOR |
| **Schema**    | USAGE, CREATE TABLE, CREATE VIEW, MODIFY |
| **Table**     | SELECT, INSERT, UPDATE, DELETE, REFERENCES |
| **Views**     | SELECT, REFERENCES |
| **Stages**    | USAGE, READ, WRITE |
| **Warehouses**| USAGE, OPERATE, MONITOR |


# 2.4 GRANT OWNERSHIP

Transfers ownership of an object.

**Note:**  
Ownership **cannot be revoked** — it must be **transferred** to another role.

### Example
```sql
GRANT OWNERSHIP ON TABLE mydb.public.orders TO ROLE data_engineer;
REVOKE CURRENT GRANTS;

```

### 3. REVOKE Privileges
The REVOKE command removes privileges previously granted to a role or share.

### 3.1 Scope of Revocation
Privileges can be revoked from:

### Roles
- Database roles
- Application roles
- Shares (data sharing)

```SQL

REVOKE <privileges>
  ON <object_type> <object_name>
  FROM ROLE <role_name>;

REVOKE SELECT ON TABLE mydb.public.orders FROM ROLE analyst;
```

### 3.3 REVOKE on Roles
Supports revoking:

- Global privileges
- Account object privileges
- Schema privileges
- Schema object privileges

### 3.4 REVOKE FROM SHARE
Used when removing objects or privileges exposed via secure data sharing.

E SELECT ON VIEW mydb.public.secure_view FROM SHARE share1;

3.5 REVOKE Notes
- Revoking a privilege that was never granted produces no error.
- Revoking USAGE on a database or schema affects all child objects.

4. Types of Privilege Categories
4.1 Global Privileges (ACCOUNT level)
Examples:
- CREATE DATABASE
- CREATE WAREHOUSE
- MANAGE GRANTS

### 4.2 Account Object Privileges
Applicable object types:
- Resource monitors
- Warehouses
- Databases
- Integrations
- External volumes

### 4.3 Schema Privileges
Examples:
- USAGE
- CREATE TABLE
- CREATE VIEW
- MODIFY
- MONITOR

### 4.4 Object Privileges
Applied at table/view/stage level:
- SELECT
- INSERT
- UPDATE
- DELETE
- REFERENCES

###5. Future Grants (Important for Automation)
Snowflake supports bulk granting privileges for future objects.
Example
- GRANT SELECT ON FUTURE TABLES IN SCHEMA mydb.public TO ROLE analyst;
Useful for:

Automated ingestion pipelines
ETL/ELT frameworks (dbt, Fivetran, etc.)

Grant types:
- INDIVIDUAL
- ALL (current objects)
- FUTURE (future objects)
- INHERITED (ALL + FUTURE)

### 6. Best Practices
### 6.1 Use GRANTS on roles, not users
Ensures clean RBAC and easier maintenance.
6.2 Avoid granting ALL PRIVILEGES
Always follow least‑privilege.
6.3 Be careful with USAGE

USAGE on a database → exposes all schemas
USAGE on a schema → exposes all objects

6.4 Validate PUBLIC Role
Remove any unintended inherited privileges.
6.5 Use role hierarchy for scalable privilege inheritance
Object roles → functional roles → SYSADMIN.

7. Helpful Commands
List all privileges on an object
SHOW GRANTS ON TABLE mydb.public.orders;
SHOW GRANTS TO ROLE analyst;
SELECT EXPLAIN_GRANTABLE_PRIVILEGES();
