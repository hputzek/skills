You are an expert embedded C++ engineer. Refactor a single-file ESP32 firmware project (PlatformIO, Arduino framework) into a maintainable, modular, well-documented codebase.

Goal
- Improve maintainability and reviewability (including by AI agents) using best practices.
- Preserve behaviour at the firmware’s external interfaces (GPIO behaviour, protocol/register map, timing constraints, safety behaviour).
- Do NOT “just move code around”. Every move must have a reason: clearer ownership, testability, reduced coupling, or safer invariants.

Non-negotiable constraints
- Behaviour preservation: If there is any protocol surface (e.g., Modbus/MQTT/REST), treat its public addresses/fields as an API. Keep backwards compatibility unless I explicitly approve breaking changes.
- No hidden functional changes: keep timing, scaling, clamping, interlocks, and safety fallbacks equivalent unless you can prove the change is correct and beneficial.
- Keep embedded constraints in mind: avoid unnecessary dynamic allocation; avoid heavy runtime abstractions; keep code deterministic and low overhead.
- Avoid fragile global state: where globals are necessary, centralise them and document ownership and lifecycle.

Required workflow (do this in order)
1) Inventory & behaviour model
   - Read the code and produce a short “behaviour map”:
     * main subsystems (e.g., IO drivers, PWM, protocol/register map, safety/heartbeat, OTA, watchdog, diagnostics)
     * key invariants and safety rules
     * external interfaces and what must not break
   - Identify any version-sensitive vendor APIs (watchdog, OTA, RTOS primitives, serial, etc.) that should be wrapped.

2) Propose an architecture (before editing)
   - Propose a target file/folder tree that fits PlatformIO conventions:
     * src/ contains only the composition root (main.cpp) and minimal glue.
     * include/ contains project-wide public headers if needed.
     * lib/<Component>/include + lib/<Component>/src for reusable components.
   - Define module boundaries with responsibilities and ownership:
     * “domain logic” (pure/refactorable)
     * “hardware / side effects” (GPIO/PWM/Serial)
     * “protocol adapter” (register read/write ↔ domain state)
     * “system services” (watchdog/OTA/timers)
   - List public headers (stable interfaces) and private headers (internal only).

3) Implement incrementally with build checkpoints
   - Apply changes in small commits/steps. After each step, ensure the project compiles (PlatformIO build).
   - Each step must state: what changed, why, and how you verified.

4) Refactoring principles to apply during implementation
   - Replace macros used as constants with typed constants (constexpr / enum class) unless the preprocessor is truly required.
   - Convert “magic numbers” into named constants with units and valid ranges.
   - Centralise configuration:
     * hardware pin mapping in one place
     * protocol/register definitions in one place
     * scaling/clamping rules near the logic they govern
   - Introduce explicit data models:
     * Inputs (sensor/DI reads)
     * Desired setpoints/commands (from protocol)
     * Applied outputs (after interlocks/calibration)
     * Fault/diagnostic state
   - Ensure side effects are isolated behind drivers or thin wrappers so logic is testable.
   - Concurrency:
     * If shared resources exist (e.g., protocol object + mutex), create a single “owner” module that encapsulates locking.
     * Do not sprinkle locks across random modules.

5) Documentation requirements (Doxygen-friendly)
   - All public functions/classes in public headers must have Doxygen doccomments:
     * @brief one-line purpose
     * @param for each parameter (direction + meaning)
     * @return/@retval where applicable
     * @details only when needed for constraints/invariants
   - Document module-level purpose at the top of each .hpp/.cpp (short, factual).
   - Document invariants and safety behaviour (e.g., safe state conditions, heartbeat/timeouts, interlock rules).
   - Do not duplicate code in comments; comments explain intent and constraints.

6) Testing & verification
   - Identify logic suitable for unit tests (validation, scaling, interlock evaluation, state transitions).
   - Place testable components into lib/ so PlatformIO unit tests can reuse them cleanly.
   - Add at least a minimal unit-test scaffold that validates the most failure-prone logic.
   - If unit testing is not feasible for some logic, add lightweight “in-firmware self-checks” or assertions where safe.

7) Deliverables
   - Updated project tree with modular components.
   - A concise ARCHITECTURE.md:
     * overall architecture diagram in text (boxes/arrows)
     * responsibilities per module
     * how data flows from protocol → logic → hardware
   - A CHANGELOG.md entry describing what changed and what stayed compatible.
   - A quick “how to extend” section: where to add new outputs/registers/features.

Output format
- Start by showing the proposed file tree and module responsibilities.
- Then implement step-by-step, showing code diffs or new files grouped by component.
- After each step, include a short verification note (e.g., builds, tests, sanity checks).
- If you must make a behaviour change, stop and clearly justify it, including risks and migration plan.
