# Product Master & Content Architecture Options

---

## 1. Original Architecture (STIBO PIM Path)

### Overview

STIBO MDM serves as the **single source of truth** for all product data, feeding both the current commerce platform (SIP / SAP Commerce) and the future platform (PCP / GDX) through the **Cloud Integration Layer (GCP Pub/Sub)**.

![Alt text](/images/stibo-current-approach.png)


### Key Characteristics & Strategic Challenges

- **New Platform + New Vendor**
  - STIBO introduced as a net-new platform
  - Requires Infosys partnership and onboarding

- **Platform Sprawl**
  - Contentstack (CMS)  
  - SAP Commerce (Hybris / SIP)  
  - STIBO (PIM)  
  → Three overlapping content & commerce platforms

- **Custom UI Build Required**
  - STIBO authoring UI requires significant customization
  - Higher delivery time and cost

- **Asset Integration Complexity**
  - Custom CDN work required to expose public image URLs
  - Not natively optimized for web delivery

- **STEP XML as Canonical Format**
  - XML is the native outbound format
  - JSON support is beta-level
  - Transformation layer required for modern APIs

---

## 2. Alternative Approach: Contentstack as Product Authority

### Overview

Contentstack—already live at Costco for CMS—becomes the **product content authority**, feeding SIP via the **Cloud Integration Layer**.  
The **Product API contract remains unchanged**, ensuring **zero frontend or mobile impact**.

![Alt text](/images/proposed-content-stack.png)


### Key Strategic Advantages

- **Existing Platform**
  - Already in production at Costco
  - No new vendor onboarding

- **Authoring Interfaces Ready**
  - Out-of-the-box authoring UI
  - Extensible where needed

- **Production-Grade APIs**
  - Proven at Costco scale
  - Designed for real-time consumption

- **Public Asset URLs**
  - Native CDN integration
  - Drag-and-drop media library

- **JSON-Native**
  - No XML transformation required

- **Zero Frontend Impact**
  - Contentstack → Pub/Sub → SIP sync
  - Product API remains unchanged

---


## 3. Future State (Contentstack Fully Migrated)

### Overview

SAP Commerce (SIP) is fully replaced by **PCP / GDX**.  
Contentstack is contains:

- Product data
- Variants
- Taxonomy
- Digital assets

Frontends consume **two specialized APIs**:

- **Composed Product API** → Contentstack
- **Commerce API** → PCP / GDX

The Cloud Integration Layer remains for system-to-system data exchange.

### Key Strategic Outcomes

- **SIP Fully Retired**
  -  GDX handles pricing, inventory, cart, checkout, orders

- **Contentstack handles**
  - All product master data authoring

- **Clean Separation**
  - Content vs. Commerce with no overlap

- **Migration Foundation**
  - Content migrated independently
  - Zero disruption during commerce transition

---

# Capabilities Review

## 1. Data Modeling Capability: Contentstack vs STIBO

### Conceptual Difference

In Contentstack, **everything is a Content Type**.  
Unlike STIBO’s predefined PIM object model, Contentstack provides a **flexible schema canvas** where products, variants, categories, bundles, and relationships are explicitly modeled.

### Capability Mapping

| SAP Commerce Concept | Contentstack Capability |
|--------------------|-------------------------|
| Product (Base) | ✅ Supported |
| Variant Product | ✅ Supported |
| Category (5-level hierarchy) | ✅ Supported |
| Classification Attributes (Brand, Size, Color) | ⚠️ Supported with customization |
| Multi-Product / Kits | ✅ Supported |
| Bundle Templates | ✅ Supported |
| Assets / Media | ✅ Strong support |


---

## 2. Classification Attributes: Core Difference

### STIBO

- True classification hierarchy
- Attribute inheritance enforced by the platform
- Selecting a category automatically exposes required attributes

### Contentstack

- No native classification inheritance
- Products do **not automatically know** which attributes apply

#### Example

**STIBO**
- Select “Men’s Tops”
- Fit, Material, Fasteners appear automatically

**Contentstack**
- Select “Men’s Tops”
- Attributes are not enforced by default

### Mitigation Strategy

Use **Contentstack Sidebar Apps** to:

- Read selected Web Category / Taxonomy
- Determine applicable Attribute Groups
- Guide authors during creation:
  - Required attributes
  - Validation before publish

---

## 3. Capabilities Summary: Contentstack vs STIBO

| Capability Area | Contentstack | STIBO STEP | Winner |
|----------------|-------------|------------|--------|
| Classification & Hierarchy | Requires custom modeling & UI guidance | Native inheritance & taxonomy | STIBO |
| Workflows & Security | Lightweight, workflow-driven governance | Strong model-enforced governance | Depends on priority |
| API Readiness | API-first, REST & GraphQL, CDN-backed | REST, middleware-oriented | Contentstack |
| Authorship UX | Out-of-the-box schema-driven UI | Often requires configuration | Contentstack |
| Data Modeling & Relationships | Flexible, reference-based modeling | Deep PIM semantics | Parity |
| Digital Asset Delivery | Public CDN URLs, web-optimized | Internal distribution focus | Contentstack |
| Mass Editing | Scripted via APIs | CSV / batch jobs | Parity |
| Data Interchange | JSON-native | XML-first | Contentstack |
| Publishing Availability | Immediate via delivery APIs | Event-based downstream sync | Contentstack |

**Decision Guidance**

- If priority = **strict governance & classification enforcement** → **STIBO**
- If priority = **speed, flexibility & modern platform alignment** → **Contentstack**

---

## 4. Workflow & Security Model Comparison

### Contentstack

- Optimized for speed and editorial agility
- Author → Reviewer → Publisher flow
- Workflows attached to content types
- Coarse-grained permissions
- Governance enforced through:
  - Process discipline
  - Schema design
  - UI conventions
  - Sidebar apps / extensions

**Example Roles**
- Product Author: write Product / Variant
- Asset Manager: manage Assets only
- Publisher: approve & publish

---

### STIBO STEP

- Optimized for strict governance and consistency
- Approvals tied to:
  - Object types
  - Classification hierarchy
  - Attribute groups
- Mandatory attributes enforced before approval
- Fine-grained permissions:
  - Object types
  - Attribute groups
  - Categories
  - Workflows

**Tradeoff**
- Higher control and consistency
- Slower change velocity
- Higher modeling overhead

---


