# 🗺️ RFC: Verifiable Asymmetric Cartography & Programmable Objectives

Following a recent discussion across the community (involving **Please No Pictures, Ocky, Econmartin, lacal, Social Assassin, beauKode, MiMiMi, Shalimar, Byron DuMont, and Scetrov**), we wanted to share a consolidated summary of how the "Fog of War" map changes are landing for builders and players, alongside a concrete proposal for **Fenris Creations (FC)** to bridge the gap between in-game exploration and 3rd-party tool building using cryptographically verifiable primitives.

---

## 1. Where the Community Stands (The 4 Core Viewpoints)

* **Static vs. Dynamic Asymmetry:** Gaming communities solve static search problems fast. Hiding static star coordinates only creates temporary friction before everything of value is logged. True, lasting information asymmetry lives in **dynamic, ephemeral system state**—scanning active mining operations, infrastructure changes, and "road closures" (destroyed/offline gates) on less-traveled routes.
* **The Explorer Profession:** Removing client-side map spoilers is great *if* there are in-game mechanisms to turn cartography into shareable, sellable, or lootable data (e.g., cache drops with nav-data fragments, tradeable intel).
* **The Tribal Intel Operator (Non-Coders):** Players running tribe intel don't want to mess with coordinate math or file scraping. They just want a frictionless way to upload discovered systems and layer **permissioned, rank-based intel** onto a shared map—ideally accessible in-flight via the updated Chromium 156 (CEF) browser.
* **The 3rd-Party Builder Reality:** Right now, map builders are relying on legacy C6 coordinate data to map `solarSystemId` and crowdsourcing the rest. Everyone agrees this crutch won't survive future cycles. Builders need official, cryptographically verifiable primitives—not unverified local log hacks—to build trustless maps and data markets.

---

## 2. The Proposal: Cryptographic Cartography & Programmable Objectives

Assuming FC will not make the full map visible by default, we don't want a static coordinate dump or an easily spoofed local loopback API. Instead, we can combine on-chain **Provable Object Data (PODs)**, **Zero-Knowledge General Purpose Circuits (GPCs)**, and **existing native UI** to make exploration verifiable, programmable, and tradeable.

### Pillar 1: Cryptographic Telemetry (PODs & GPCs)

Instead of exposing raw `(X, Y, Z)` coordinates or unverified plaintext logs, the FC game server acts as an issuer of **Provable Object Data (PODs)**—cryptographically signed, self-verifying data objects handed to the client upon jump or scan:

* **Signed Spatial Telemetry:** When a player jumps or activates an observation module, the server issues a signed POD containing `CharacterID`, `FromSystemID`, `ToSystemID`, `RelativeDistance/Bearing`, `StarAttributes`, and `Timestamp`.
* **Dynamic Scan PODs:** Scanning a system issues a signed POD attesting to the current snapshot of Smart Assemblies, mining progress, or gate statuses in that system at that exact timestamp.
* **Zero-Knowledge Intel via GPCs:** Because PODs can be verified inside **General Purpose Circuits (GPCs)**, players and tribes can generate Zero-Knowledge proofs about their discoveries *without revealing the underlying raw data*. 
  * *Example:* A scout can mathematically prove to a data broker contract that they possess fresh scan PODs for a 5-jump route connecting System A to System B (or prove an enemy assembly exists within X lightyears) **without revealing the exact intermediate systems or coordinates** until the bounty is paid.
* **Lootable Map Fragments:** NPC caches/wrecks can drop signed nav-data PODs (or on-chain objects) that unlock verifiable system telemetry when claimed or traded.

### Pillar 2: The "Server Attestation -> Client Relay" On-Chain Pattern

To allow these off-chain cryptographic proofs to interact with Sui smart contracts without costing FC any gas:

1. **Off-Chain Issuance:** FC's server signs the event receipt (POD / Ed25519 attestation) and passes it to the player's local client/POD store.
2. **Client Submission (On-Chain or Off-Chain):**

   * **Off-Chain Tribal Maps:** The player passes the POD (or a redacted GPC proof) directly to their tribe's permissioned map backend via the CEF browser. The map verifies FC's signature instantly—zero spoofing possible.
   * **On-Chain Objectives & Markets:** The client bundles the server-signed attestation (or ZK proof) into a Sui transaction signed by the player's wallet and submits it to a **Cartography or Objective Smart Contract**.

> [!NOTE]
> Optionally these may be grouped into a package, because no one wants to have 1m PODs to share.

### Pillar 3: Repurpose the Top-Left Tutorial UI + CEF Overlay

Non-technical players need a seamless way to interact with verifiable intel in-game without manual file uploads:

* **Bindable Objective Tracker:** Allow players to point their native **top-left Tutorial/Objective UI** at a custom Smart Contract address. Tribes can push live recon missions (*"Submit a fresh Scan POD for System X"*, *"Verify route status to System Y"*) directly to their pilots' HUDs. When the server issues the matching POD, the player clicks "Complete" in the top-left tracker to submit the cryptographic proof via their wallet.
* **Flight-View CEF Overlay & POD Bridge:** With the in-client CEF updated to Chromium 156, enabling a toggleable flight-view overlay with a `window.ef` JS bridge that exposes the player's local POD store allows community maps to ingest verifiable jump/scan proofs in real time and render permissioned tribal intel directly over the flight screen.

---

## 3. Summary of "Asks" for FC

1. **Cryptographic Telemetry Primitives (PODs):** Have the game server issue signed **Provable Object Data (PODs)** containing `solarSystemId`, star metadata, dynamic scan snapshots, and **relative distances/bearings** upon jump or scan—eliminating both C6 data scraping and spoofable plaintext logs.
2. **GPC / On-Chain Verification Support:** Publish FC's attestation public keys so 3rd-party off-chain indexers, GPC zero-knowledge circuits, and Sui Move smart contracts can natively verify player-submitted discovery proofs.
3. **HUD & CEF Integration:** Allow the top-left Objective Tracker to bind to custom smart contracts for proof submission, and expose the client's signed POD buffer to a persistent flight-view Chromium 156 IGB overlay.
