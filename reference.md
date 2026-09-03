# Reference
## Addons
<details><summary><code>client.addons.<a href="src/billkit/addons/client.py">attach_addon</a>(...) -> AttachAddonResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Validates that the add-on key exists in the tenant's schema, attaches it to
the subject's existing assignment in the store, and invalidates the subject's
cache entries.

- If the request body is missing or malformed: returns 400 (via Axum rejection).
- If subject_id or addon_key is empty: returns 400.
- If the tenant has no schema uploaded: returns 422.
- If the add-on key does not exist in the schema: returns 422.
- If the subject has no existing assignment: returns 404.
- If a store or cache error occurs: returns 500.
- On success: returns 200 with the updated add-on list.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.addons.attach_addon(
    addon_key="addon_key",
    subject_id="subject_id",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**addon_key:** `str` 
    
</dd>
</dl>

<dl>
<dd>

**subject_id:** `str` 
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.addons.<a href="src/billkit/addons/client.py">detach_addon</a>(...)</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Removes the given add-on from the subject's existing assignment in the store
and invalidates the subject's cache entries. Tenant is resolved from
`X-Api-Key` via the auth middleware.

- If subject_id or addon_key is empty: returns 400.
- If the subject has no existing assignment: returns 404.
- If a store error occurs: returns 500.
- On success: returns 204 No Content, whether or not the add-on was
  actually attached (idempotent, matching `DELETE /assignments/{subject_id}`).
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.addons.detach_addon(
    subject_id="subject_id",
    addon_key="addon_key",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**subject_id:** `str` — The subject identifier
    
</dd>
</dl>

<dl>
<dd>

**addon_key:** `str` — The add-on key to detach
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Assignments
<details><summary><code>client.assignments.<a href="src/billkit/assignments/client.py">create_assignment</a>(...) -> CreateAssignmentResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Assigns a plan to a subject. Validates that the plan key exists in the tenant's
schema, writes the assignment to the store, and invalidates the subject's cache entries.

- If the request body is missing or malformed: returns 400.
- If the subject_id exceeds 256 characters: returns 400.
- If the tenant has no schema uploaded: returns 422.
- If the plan key does not exist in the schema: returns 422.
- If a store or cache error occurs: returns 500.
- On success: returns 201 with the assignment details.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.assignments.create_assignment(
    plan_key="plan_key",
    subject_id="subject_id",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**plan_key:** `str` 
    
</dd>
</dl>

<dl>
<dd>

**subject_id:** `str` 
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.assignments.<a href="src/billkit/assignments/client.py">delete_assignment</a>(...)</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Removes the subject assignment from the store and invalidates the subject's
cache entries. Tenant is resolved from `X-Api-Key` via the auth middleware.

- If subject_id is empty: returns 400.
- If a store error occurs: returns 500.
- On success: returns 204 No Content (regardless of whether the assignment existed).
  The `max_subjects` self-metering counter is only decremented when an
  assignment actually existed, so repeated or no-op deletes cannot drive
  the counter negative.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.assignments.delete_assignment(
    subject_id="subject_id",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**subject_id:** `str` — The subject identifier to unassign
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Billing
<details><summary><code>client.billing.<a href="src/billkit/billing/client.py">change_plan</a>(...) -> ChangePlanResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Allows a portal-authenticated subject to switch their plan. This is the
immediate-switch path only: it handles the case where billing is not configured
in the schema, or where the old and new plan have equal prices (lateral move).

Upgrade/downgrade branches (with proration math and Stripe charges) are
implemented in a later task and will extend this handler.

## Authentication
Requires portal JWT (Bearer token). The subject identity comes from
the `PortalContext` extension (injected by `portal_auth_middleware`).

## Request Body
```json
{ "plan_key": "pro" }
```

## Response (200)
```json
{
  "subject_id": "user_123",
  "previous_plan_key": "starter",
  "new_plan_key": "pro",
  "changed_at": "2024-01-15T12:30:00Z",
  "effective": "immediate",
  "proration_charge_cents": 0
}
```

## Errors
- 400: Empty `plan_key` or already on the requested plan
- 401: Missing or invalid portal token
- 404: Subject not found or plan key not in schema
- 500: Internal error
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.billing.change_plan(
    plan_key="plan_key",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**plan_key:** `str` — The plan key to switch to.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.billing.<a href="src/billkit/billing/client.py">create_portal_token</a>(...) -> CreatePortalTokenResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Generates a signed JWT that grants a specific subject access to the hosted
billing portal. The token is scoped to both the tenant and the subject,
preventing cross-tenant or cross-subject access.

## Authentication
Requires API key authentication (X-Api-Key header). Only the tenant's
backend should call this endpoint.

## Request Body
```json
{ "subject_id": "user_123" }
```

## Response (200)
```json
{
  "token": "eyJ...",
  "portal_url": "https://billing.billkit.io/acme-corp/manage?token=eyJ...",
  "expires_at": "2024-01-15T12:30:00Z"
}
```

## Errors
- 400: Missing or empty `subject_id`
- 404: Subject not found or not assigned to this tenant
- 500: Internal error (JWT signing failure, store error)
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.billing.create_portal_token(
    subject_id="subject_id",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**subject_id:** `str` — The subject (end user) to generate a portal token for.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.billing.<a href="src/billkit/billing/client.py">portal_list_invoices</a>(...) -> PortalListInvoicesResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Scoped to the subject identified in the portal token. Cannot see other subjects' invoices.
Supports cursor-based pagination with portal-specific limits (default 50, max 200).

Query parameters:
- `limit` — maximum number of items per page (1–200, default 50)
- `cursor` — opaque pagination cursor from a previous response
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.billing.portal_list_invoices()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**limit:** `typing.Optional[int]` — Maximum number of items to return per page.
    
</dd>
</dl>

<dl>
<dd>

**cursor:** `typing.Optional[str]` — Opaque pagination cursor from a previous response's `next_cursor` field.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Entitlements
<details><summary><code>client.entitlements.<a href="src/billkit/entitlements/client.py">check</a>(...) -> GateResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Authenticates via the auth middleware (X-Api-Key), resolves the entitlement
configuration from cache/store, invokes the gate decision evaluator, increments
the usage counter in cache for metered features, and returns a `GateResponse`.

- If subject_id or feature_code is empty: returns 400.
- If feature_code not found in schema: returns 404.
- If subject has no plan assigned: returns 200 with `decision: "no_plan"`.
- If a store error occurs: returns 500.
- On success: returns 200 with the gate decision.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.entitlements.check(
    feature_code="feature_code",
    subject_id="subject_id",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**feature_code:** `str` 
    
</dd>
</dl>

<dl>
<dd>

**subject_id:** `str` 
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Contracts
<details><summary><code>client.contracts.<a href="src/billkit/contracts/client.py">apply_contract</a>(...) -> ApplyContractResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Validates that the contract key exists in the tenant's schema, applies it to
the subject's existing assignment in the store, and invalidates the subject's
cache entries.

- If the request body is missing or malformed: returns 400 (via Axum rejection).
- If subject_id or contract_key is empty: returns 400.
- If the tenant has no schema uploaded: returns 422.
- If the contract key does not exist in the schema: returns 422.
- If the subject has no existing assignment: returns 404.
- If a store or cache error occurs: returns 500.
- On success: returns 200 with the applied contract details.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.contracts.apply_contract(
    contract_key="contract_key",
    subject_id="subject_id",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**contract_key:** `str` 
    
</dd>
</dl>

<dl>
<dd>

**subject_id:** `str` 
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.contracts.<a href="src/billkit/contracts/client.py">detach_contract</a>(...)</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Clears any custom contract from the subject's existing assignment in the
store and invalidates the subject's cache entries. Tenant is resolved from
`X-Api-Key` via the auth middleware.

- If subject_id is empty: returns 400.
- If the subject has no existing assignment: returns 404.
- If a store error occurs: returns 500.
- On success: returns 204 No Content, whether or not a contract was
  actually applied (idempotent, matching `DELETE /assignments/{subject_id}`).
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.contracts.detach_contract(
    subject_id="subject_id",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**subject_id:** `str` — The subject identifier
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Invoices
<details><summary><code>client.invoices.<a href="src/billkit/invoices/client.py">list_invoice_failures</a>(...) -> ListInvoiceFailuresResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Lists invoice-failure markers for the authenticated tenant, most recent
failure first, with optional filtering by `subject_id` and `exhausted`.
Supports cursor-based pagination.

Query parameters:
- `subject_id` — filter by subject
- `exhausted` — `true`/`false` to filter by retry-budget status
- `limit` — maximum number of items per page (1–1000, default 100)
- `cursor` — opaque pagination cursor from a previous response
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.invoices.list_invoice_failures()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**subject_id:** `typing.Optional[str]` — Filter by subject ID
    
</dd>
</dl>

<dl>
<dd>

**exhausted:** `typing.Optional[bool]` — Filter by retry-budget status (true = exhausted)
    
</dd>
</dl>

<dl>
<dd>

**limit:** `typing.Optional[int]` — Maximum items per page (1–1000, default 100)
    
</dd>
</dl>

<dl>
<dd>

**cursor:** `typing.Optional[str]` — Opaque pagination cursor from previous response
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.invoices.<a href="src/billkit/invoices/client.py">list_invoices</a>(...) -> ListInvoicesResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Lists end-user invoices for the authenticated tenant with optional filtering
by subject_id, status, and date range. Supports cursor-based pagination.

Query parameters:
- `subject_id` — filter by subject
- `status` — filter by invoice status (open, paid, past_due, uncollectible).
  Any other value is rejected with a 400.
- `start_date` — RFC 3339 datetime; only invoices overlapping this date or later.
  Must be a valid RFC 3339 timestamp, or the request is rejected with a 400.
- `end_date` — RFC 3339 datetime; only invoices overlapping this date or earlier.
  Must be a valid RFC 3339 timestamp, or the request is rejected with a 400.
- `limit` — maximum number of items per page (1–1000, default 100)
- `cursor` — opaque pagination cursor from a previous response
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.invoices.list_invoices()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**subject_id:** `typing.Optional[str]` — Filter by subject ID
    
</dd>
</dl>

<dl>
<dd>

**status:** `typing.Optional[str]` — Filter by status (open, paid, past_due, uncollectible)
    
</dd>
</dl>

<dl>
<dd>

**start_date:** `typing.Optional[str]` — RFC 3339 start date filter
    
</dd>
</dl>

<dl>
<dd>

**end_date:** `typing.Optional[str]` — RFC 3339 end date filter
    
</dd>
</dl>

<dl>
<dd>

**limit:** `typing.Optional[int]` — Maximum items per page (1–1000, default 100)
    
</dd>
</dl>

<dl>
<dd>

**cursor:** `typing.Optional[str]` — Opaque pagination cursor from previous response
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.invoices.<a href="src/billkit/invoices/client.py">list_platform_invoices</a>() -> ListPlatformInvoicesResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Lists Billkit's platform invoices for the authenticated tenant (charges from
Billkit to the tenant). Returns all platform invoices sorted by `created_at`
descending (most recent first).

Platform invoices are bounded (~12-24 per year) so no pagination is needed.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.invoices.list_platform_invoices()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.invoices.<a href="src/billkit/invoices/client.py">get_revenue</a>() -> RevenueResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Aggregates revenue metrics from all end-user invoices for the authenticated
tenant. Returns totals and a month-over-month breakdown grouped by
`billing_period_end`.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.invoices.get_revenue()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.invoices.<a href="src/billkit/invoices/client.py">preview_invoice</a>(...) -> InvoicePreviewResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Calculates what the next invoice would look like for a subject based on
their current usage, plan assignment, and schema billing configuration.

Returns:
- 200 with the preview if billable usage exists
- 404 if the subject is not found or has no plan assignment
- 422 if no billing config or Connect account is configured
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.invoices.preview_invoice(
    subject_id="subject_id",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**subject_id:** `str` — The subject identifier
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## API Keys
<details><summary><code>client.api_keys.<a href="src/billkit/api_keys/client.py">list_keys</a>() -> ListKeysResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Lists all named API keys for the tenant. Keys are masked (only prefix shown).
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.api_keys.list_keys()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.api_keys.<a href="src/billkit/api_keys/client.py">create_key</a>(...) -> CreateKeyResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Creates a new named API key for the tenant. The raw key value is returned
once in the response and never stored — only its SHA-256 hash is persisted.

Limits tenants to MAX_KEYS_PER_TENANT keys.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.api_keys.create_key(
    name="name",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**name:** `str` — Human-readable name for the key (e.g., "Production", "Staging").
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.api_keys.<a href="src/billkit/api_keys/client.py">rotate_key</a>() -> RotateKeyResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Generates a new API key, updates the tenant record in the store, replaces
the API key reference, updates cache mappings, and invalidates the old key.
After rotation, the old key will be rejected with 401 on subsequent requests.

- If the tenant record cannot be found: returns 500 (should not happen since
  auth middleware already resolved the tenant).
- If a store or cache error occurs: returns 500.
- On success: returns 200 with the new API key (shown only once).
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.api_keys.rotate_key()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.api_keys.<a href="src/billkit/api_keys/client.py">delete_key</a>(...) -> RevokeKeyResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Revokes (deletes) a specific API key by its key_id. Removes both the
tenant-scoped record and the global API key reference, and invalidates
the key in cache.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.api_keys.delete_key(
    key_id="key_id",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**key_id:** `str` — The key identifier to revoke
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Pricing
<details><summary><code>client.pricing.<a href="src/billkit/pricing/client.py">get_pricing</a>() -> PricingResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Returns the authenticated tenant's pricing information formatted for a
pricing page. Plans are sorted by price ascending, features are ordered
by type (boolean → metered → stateful → attribute), and all values are
formatted for direct display.

Supports conditional requests via `ETag` / `If-None-Match` headers.
Returns 304 Not Modified when the schema has not changed.

Responses are cached in Valkey with a 15-minute sliding TTL. Cache is
checked before DynamoDB to minimize latency for hot pricing pages.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.pricing.get_pricing()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Schema
<details><summary><code>client.schema.<a href="src/billkit/schema/client.py">get_schema</a>(...) -> str</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Retrieves the tenant's own schema document from the store.
Tenant is resolved from `X-Api-Key` via the auth middleware.

Accepts an optional `?format=yaml` or `?format=json` query parameter.
Defaults to JSON (the canonical storage format). When `yaml` is requested,
the stored JSON is converted to YAML before returning.

- If the tenant has a schema: returns 200 with the schema document body.
- If the tenant has no schema: returns 404 with `{ "error": "schema not found" }`.
- If a store error occurs: returns 500 with `{ "error": "internal error" }`.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.schema.get_schema()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**format:** `typing.Optional[GetSchemaRequestFormat]` — Output format: "json" (default) or "yaml".
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.schema.<a href="src/billkit/schema/client.py">put_schema</a>(...) -> ValidationErrorResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Validates, persists to store, and invalidates all tenant cache entries.
Tenant is resolved from `X-Api-Key` via the auth middleware.

Before persisting, the new document is checked for referential integrity
against the tenant's existing subject assignments: if any assignment
references a `plan_key`, `contract_key`, or add-on key that would no
longer exist in the new document, the upload is rejected (422) so that
assignments never silently go stale. Pass `?force=true` to bypass this
check and persist anyway (e.g. when reassigning affected subjects out of
band).

- If the body is empty: returns 400.
- If the document has validation errors: returns 200 with `{ "valid": false, "errors": [...] }` (not persisted).
- If persisting would orphan subject assignments and `force` is not set: returns 422 with `{ "valid": false, "errors": [...] }` (not persisted).
- If the document is valid: persists to store, invalidates cache, returns 200 with `{ "valid": true, "errors": [] }`.
- If a store or cache error occurs: returns 500.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.schema.put_schema()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**force:** `typing.Optional[bool]` 

When `true`, bypass the referential-integrity check against existing
subject assignments and persist the schema even if doing so would orphan
a plan, contract, or add-on key that subjects are still assigned to.
Defaults to `false`.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.schema.<a href="src/billkit/schema/client.py">validate_schema</a>(...) -> ValidationErrorResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Accepts a raw schema document body (YAML or JSON), runs the full validator
(structural + semantic), and returns a `ValidationErrorResponse` without
persisting anything.

Accepts an optional `?format=json` or `?format=yaml` query parameter.
When provided and the schema is valid, the response includes a `document`
field containing the schema serialized in the requested format.
Defaults to no document in the response (backward-compatible).

- If the document is valid: returns 200 with `{ "valid": true, "errors": [] }` (and optionally `"document": "..."`).
- If the document has validation errors: returns 200 with `{ "valid": false, "errors": [...] }`.
- If the body is empty: returns 400.

This endpoint is protected by the auth middleware (X-Api-Key), but does not
use the tenant context since validation is stateless.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.schema.validate_schema()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**format:** `typing.Optional[ValidateSchemaRequestFormat]` — Output format: "json" (default) or "yaml".
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Subjects
<details><summary><code>client.subjects.<a href="src/billkit/subjects/client.py">list_subjects</a>(...) -> SubjectsResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Returns all subject assignments for the authenticated tenant.
The tenant is resolved from `X-Api-Key` via the auth middleware.

- If no subjects exist: returns HTTP 200 with an empty `subjects` array.
- If authenticated with the system API key: returns all subjects under the system tenant.
- If no valid API key is provided: returns HTTP 401 (handled by auth middleware).
- If a DynamoDB failure occurs: returns HTTP 500.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.subjects.list_subjects()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**limit:** `typing.Optional[int]` — Maximum number of items to return per page.
    
</dd>
</dl>

<dl>
<dd>

**cursor:** `typing.Optional[str]` — Opaque pagination cursor from a previous response's `next_cursor` field.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.subjects.<a href="src/billkit/subjects/client.py">register_subject</a>(...) -> RegisterSubjectResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Registers a subject with optional email and name. If an email is provided
and the tenant has an active Stripe Connect account, creates a Stripe Customer
on the connected account and marks the subject as "billable".

If the subject already exists (re-registration), updates the Stripe Customer's
email/name rather than creating a duplicate.

`plan_key` is validated against the tenant's schema, mirroring `POST /assignments`:
- If `plan_key` is provided but does not exist in the schema: returns 422.
- If `plan_key` is omitted, it defaults to the schema's `default_plan` (not the
  literal string `"free"`). If the schema defines no `default_plan`: returns 422.
- If the tenant has no schema uploaded at all: returns 422.

- If subject_id is missing or too long: returns 400.
- If the subject already exists: updates email/name/plan_key/Stripe Customer (idempotent).
- If no Stripe Connect account is active: subject is stored without Stripe Customer.
- If Stripe API fails: returns 502 (subject is NOT partially created).
- On success: returns 201 (new) or 200 (re-registration).
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.subjects.register_subject(
    subject_id="subject_id",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `RegisterSubjectRequest` 
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.subjects.<a href="src/billkit/subjects/client.py">register_subjects_batch</a>(...) -> BatchRegisterResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Registers multiple subjects in a single request. Creates Stripe Customers
for each subject that has an email and where the tenant has active Connect.

- If the batch exceeds 100 subjects: returns 400.
- Individual failures are reported per-subject (no rollback).
- On success: returns 200 with results array.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi, RegisterSubjectRequest

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.subjects.register_subjects_batch(
    subjects=[
        RegisterSubjectRequest(
            subject_id="subject_id",
        )
    ],
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**subjects:** `typing.List[RegisterSubjectRequest]` 
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.subjects.<a href="src/billkit/subjects/client.py">get_subject</a>(...) -> SubjectDetailResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Returns a single subject's details including plan, billing status,
payment method status, and Stripe Customer ID.

- If the subject does not exist: returns 404.
- On success: returns 200 with full subject details.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.subjects.get_subject(
    subject_id="subject_id",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**subject_id:** `str` — The subject identifier
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Usage
<details><summary><code>client.usage.<a href="src/billkit/usage/client.py">set_usage</a>(...) -> SetUsageResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Sets the absolute value of a stateful feature counter. Enforces limits at write time:
if the requested value exceeds the configured max, the request is rejected with 409.
Writes directly to cache (bypassing the increment buffer) and publishes a `UsageSetEvent`
to SQS for durable persistence.

- If `subject_id` or `feature_code` is empty: returns 400.
- If `feature_code` not found in schema: returns 404.
- If feature type is not Stateful: returns 422.
- If subject has no plan assigned: returns 409 with reason "no_plan".
- If value exceeds max: returns 409 with reason "value exceeds stateful feature limit".
- If cache write fails: returns 503.
- On SQS publish failure: still returns 200 (fire-and-forget).
- On success: returns 200 with accepted response.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.usage.set_usage(
    subject_id="subject_id",
    feature_code="feature_code",
    value=1000000,
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**subject_id:** `str` — The subject whose counter is being set
    
</dd>
</dl>

<dl>
<dd>

**feature_code:** `str` — The stateful feature code (e.g., "seats")
    
</dd>
</dl>

<dl>
<dd>

**value:** `int` — The absolute counter value to set.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.usage.<a href="src/billkit/usage/client.py">get_usage</a>(...) -> UsageResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Returns aggregated usage statistics for the authenticated tenant.
Includes per-feature consumption totals and cadence window metadata.

Plan-specific fields (limit, cadence, overage, warning) are intentionally
omitted from the aggregated view because they are per-plan values that
cannot be meaningfully resolved at the tenant level (see issue #224).

When `?per_subject=true` is passed, returns per-subject usage breakdowns
including each subject's feature counters with plan-specific limits
resolved from the schema.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.usage.get_usage()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**per_subject:** `typing.Optional[bool]` — When true, returns per-subject usage breakdowns
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Tenants
<details><summary><code>client.tenants.<a href="src/billkit/tenants/client.py">get_tenant_me</a>() -> TenantMeResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Protected by the auth middleware. Returns metadata about the authenticated
user's tenant. The tenant_id is resolved by the auth middleware, either from:
1. The JWT `custom:tenant_id` claim (if present), or
2. The `USER#{sub}` → `tenant_id` mapping in DynamoDB (the common path).

This endpoint then verifies the tenant record itself exists in DynamoDB and
returns its metadata.

- 200: tenant record found — returns tenant metadata (tenant_id, org_id, plan_key, created_at).
- 404: tenant_id was resolved from the user link but the TenantRecord is missing.
  This can happen after a partial database reset or data corruption.
- 401: (from auth middleware) user has no tenant association — either no
  `custom:tenant_id` claim and no USER#{sub} link in DynamoDB. This is the
  normal state for users who haven't provisioned yet, or after a full DB reset.
- 500: unexpected store error.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from billkit import BillkitApi

client = BillkitApi(
    api_key="<value>",
    base_url="https://yourhost.com/path/to/api",
)

client.tenants.get_tenant_me()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

