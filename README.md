# Partenza

> A modular, pluggable init interface for Linux — compatible with systemd, but implementation-agnostic.

## 🌱 Motivation

**systemd** is the de facto standard for Linux init systems. It solved real-world problems with fragile boot processes, inconsistent service management, and missing APIs. It brought us reliable units, socket activation, predictable boot order, and uniform interfaces.

But its dominance comes at a cost:

- It is monolithic, and most components are tightly coupled.
- It assumes its own presence throughout the userspace stack.
- It creates high barriers to alternatives (GNOME, KDE, and others assume systemd APIs).
- It discourages diversity and experimentation in how systems start and manage services.

Many recognize these issues — but we don’t need to “remove” systemd to fix them.  
We need to treat it like what it already is: **a powerful implementation of a missing standard**.

---

## 🧭 Lessons from History

### 📡 TCP/IP — Standards After Adoption

TCP/IP became the foundation of modern networking *before* formal RFCs were finalized. Its utility preceded its specification. Once it proved essential, it was written down and standardized.  
**Lesson**: A useful system, even if messy at first, can become a standard when the community sees value.

### 🖥️ Wayland & Weston — Reference Implementations Matter

Wayland’s protocol offered a cleaner alternative to X11, but progress was slow until **Weston** demonstrated the protocol in action. It proved that the design could work in practice, guiding others to implement compositors around it.  
**Lesson**: Even with a clean protocol, you need a working proof to validate ideas and gain adoption.

### 🎮 Vulkan — Pluggable Interfaces via ICD

Vulkan separates the API from its providers using **ICD (Installable Client Driver) files**, allowing users to switch between GPU vendors at runtime.  
**Lesson**: A well-defined, pluggable API interface allows for flexibility and vendor neutrality without sacrificing performance or features.

---

## 💡 The Vision

A modular specification for system init and user-space management, compatible with systemd but **agnostic to implementation**.

### Core Ideas

- 🧩 **Pluggable Providers**: Logging, service management, device initialization, session daemons — all replaceable.
- 📦 **Loader Abstraction**: A lightweight core dispatches init API calls to user-selected providers.
- 📜 **Declarative Config**: JSON or similar files (like Vulkan ICDs) declare which provider handles each interface.
- 🔒 **Security-first**: Providers must be sandboxed, authenticated, and permission-aware.
- 📚 **API Contracts**: Based on systemd's proven D-Bus interface structure, but open to multiple implementations.

---

## ✅ Benefits

- Works with **existing software** that expects systemd APIs.
- Enables **true choice** in user-space system components.
- Encourages **experimentation** without fragmentation.
- Powers **custom Linux distributions** with unique needs.
- Improves **portability** and long-term maintainability.

---

## ⚖️ Trade-Offs

- Slight overhead in indirection/config parsing.
- Needs at least one working implementation (like Weston for Wayland).
- May face resistance from systemd-integrated environments.

---

## 🔭 Where to Begin

- Start from existing alternatives like [InitWare](https://github.com/initware/initware) or [Dinit](https://github.com/davmac314/dinit).
- Use them as **proofs of concept**, not final solutions.
- Extract the API boundaries and document them — *after* something useful runs.
- Build a small loader to dynamically route init/service APIs.
- Focus first on minimal stack: logging, service start/stop, shutdown.

---

## 🧪 How to Participate

This is a **call to action**, not a finished framework. We’re building a foundation.

You can help by:
- Reviewing this idea and giving feedback.
- Filing issues with questions, design notes, or research.
- Sharing this with systems developers, distro maintainers, and OSS contributors.
- Prototyping interfaces and writing compatibility shims.
- Writing the spec—or testing the first steps toward one.
