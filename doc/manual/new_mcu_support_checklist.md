# New MCU support checklist

Use this checklist for every pull request that adds or expands an MCU family.
Record each applicable item in the pull request description. Do not mark a
family as verified merely because it builds or one operation succeeds.

## 1. Scope and source evidence

- [ ] State the exact manufacturer, family, part number(s), package/board, and
      intended debug transports.
- [ ] Link the public reference manual, datasheet, errata, and any vendor
      OpenOCD source used as evidence.
- [ ] Record the CPU architecture, debug-port ID, device ID, revision ID,
      Flash-size-register address, Flash base, SRAM/work-area constraints, and
      reset requirements.
- [ ] State whether the change supports a whole family or only named, tested
      part numbers. Do not use a family-wide claim without family-wide evidence.

## 2. Target configuration

- [ ] Add or update `tcl/target/<vendor>/<family>.cfg` with a stable, public
      target and Flash-driver name.
- [ ] Define and validate SWD/JTAG transport, DAP/TAP creation, expected ID,
      target core, work area, adapter speed, reset method, and watchdog-halt
      settings as applicable.
- [ ] Add the Flash bank with the correct base address, size probing or safe
      override, bus width, and target name.
- [ ] Keep script comments consistent with the actual driver and documented
      support boundary.

## 3. Flash-driver implementation

- [ ] Add a dedicated Flash-driver entry in source, `driver.h`, and
      `drivers.c`; a public driver name must not rely on a Tcl alias.
- [ ] Implement, or explicitly constrain and document, any shared controller
      logic. Reuse code only when the relevant register behavior is verified.
- [ ] Identify the device using a combination that cannot collide with another
      family (for example, device ID plus revision and core type).
- [ ] Validate Flash geometry: size, erase-sector/page layout, programming
      granularity, bank layout, and maximum supported capacity.
- [ ] Validate protection, option-byte/security, lock/unlock, mass-erase, and
      blank-check behavior. Mark unsupported operations clearly; never expose
      an unverified destructive command as supported.
- [ ] Add driver-specific commands only when their semantics are verified.
      Otherwise document use of the generic `flash` commands.

## 4. Documentation and packaging

- [ ] Add a formal driver entry and configuration example to `doc/openocd.texi`.
- [ ] Update the README support matrix with exact parts, real adapter/board,
      completed test stages, and an honest status.
- [ ] Update release notes or migration notes when the addition changes an
      existing driver, target script, or user-facing configuration.
- [ ] Confirm `make install` installs the target script and that the installed
      binary can find it without a source-tree search path.

## 5. Validation evidence

- [ ] Run a clean configure/build appropriate to the changed feature, plus
      `git diff --check`.
- [ ] Use physical hardware to record probe output: adapter, transport, CPU,
      DAP ID, device/revision ID, detected Flash size, and page/sector layout.
- [ ] On the real target, perform erase/program, verify, reset/run, and a
      post-reset connectivity check using a representative image.
- [ ] Test GDB attach, halt/resume, and breakpoints before using the status
      `Verified`; otherwise use `Programming verified` or `Experimental`.
- [ ] Test protection and recovery flows only if those features are claimed.
      Keep the before/after evidence outside the source tree when it contains
      product firmware or sensitive data.

## 6. Review and publication

- [ ] Keep the pull request limited to OpenOCD source, scripts, documentation,
      and reproducible test evidence; do not include unrelated application or
      board-project configuration.
- [ ] Verify that every target-script name, public driver name, README claim,
      and manual example matches exactly.
- [ ] Review the final diff and commit history; ensure no generated binaries,
      credentials, proprietary firmware, or unrelated files are included.
- [ ] Keep the change on this fork unless a separate, explicit decision is made
      to contribute upstream.
