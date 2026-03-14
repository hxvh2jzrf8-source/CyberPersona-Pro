# Copilot / AI Agent Instructions for CyberPersona Pro

This file describes the minimum, actionable context an AI coding agent needs to be productive in this repository.

Summary
- Project: CyberPersona Pro (GUI-based fake identity generator). See `README.md` for high-level goals.
- Tech stack: Python GUI (Tkinter), `faker` for personas, temp-email integration via `mail.tm` (or equivalent), packaged into a Windows `.exe` via `PyInstaller`.

What to know first
- The repository frequently ships as a packaged Windows binary (`.exe`) in Releases—source may be limited in the repo. Confirm whether Python source files exist before making code edits.
- The GUI uses Tkinter conventions (frames, callbacks). Expect a single main entrypoint script (common names: `main.py` or `app.py`). If those files are absent, search for any `.py` files that create `Tk()` or call `mainloop()`.

Build & run (project-specific)
- Typical local run (if source is present): create a venv, install deps, run the entrypoint. Example sequence:

  python -m venv .venv
  .venv\Scripts\activate
  pip install -r requirements.txt
  python path\to\entrypoint.py

- Building Windows executable (how releases are produced): use PyInstaller with `--onefile`. Example:

  pip install pyinstaller
  pyinstaller --onefile --noconsole --name "CyberPersona-Pro" path\to\entrypoint.py

- If a GUI app does not show a console and you need logs while debugging, build without `--noconsole` or run the script directly with Python.

Important dependencies & integrations
- `faker` is used for persona data—look for calls to `Faker()` and locale configuration.
- Temporary-email integration: look for a `mail.tm` wrapper or direct HTTP calls to the mail.tm API. Search for `mail.tm`, `mailtm`, `mail` or API endpoints like `https://api.mail.tm`.
- Network activity: the app requires internet for temp inbox + OTP polling. Expect polling loops or background threads for inbox updates.

Project conventions and patterns (discoverable)
- GUI code will follow Tkinter patterns: widget creation, `grid`/`pack` layout, and callback handlers attached to buttons. Prefer small, focused edits to avoid breaking the event loop.
- Long-running network calls should run off the main thread. If adding network code, follow existing pattern (threading or background polling) rather than blocking callbacks.
- Packaging: releases are produced via PyInstaller. Keep CLI args and main guard (`if __name__ == '__main__':`) intact for packaging.

What to look for when editing
- Entry points: locate the Python file that creates `Tk()` and calls `mainloop()`—this is the canonical place to start for UI changes.
- Background workers: identify any thread, Timer, or `after()` usage that polls for emails; match scheduling mechanics when extending polling logic.
- Network error handling: follow the repo's existing pattern for retry/backoff. If none found, prefer conservative short retries and visible UI error messages.

Testing and debugging notes
- There are no automated tests in the repository by default. Validate changes by running the GUI locally on Windows and exercising the temp-mail flow.
- For fast iteration, run the Python entrypoint instead of building an exe.

Files to inspect first
- `README.md` — explains architecture and release/binary expectations.
- Any `.py` files that create a `Tk()` root or import `faker`/`mail.tm`.

When to ask the maintainer
- If the repository only contains the `.exe` (no source), ask for source or instructions to reproduce the build.
- If you need API credentials for temp-mail providers, ask whether test credentials or mocks are preferred.

If anything in this guidance is unclear or you want the agent to follow different constraints, tell me which areas to expand or clarify.
