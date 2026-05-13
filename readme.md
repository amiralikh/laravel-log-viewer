# Log <span style="color:#b71c2c">Reader</span>

> A small offline tool for reading Laravel logs in restricted environments where Sentry, Flare, or Bugsnag aren’t an option.

---

## The reality

### Most of the time, we reach for **Sentry**.

Sentry, Flare, Bugsnag, and Datadog have spoiled us. Stack traces arrive in our inbox, grouped, deduplicated, with breadcrumbs and release tags. For most projects, that’s the right answer.

But not every project lets us reach for the right answer. Some clients operate under information-security policies that forbid sending application data to third-party services. Hospitals, banks, public-sector systems  anyone whose data residency or compliance posture rules out a SaaS error tracker. In those environments, the logs live on the server and stay there.

> **No external telemetry. No outbound connections from the app servers. Any errors must be inspected on-site, in the existing log files.**  
> <sub>Typical clause in a regulated client’s security policy</sub>

And so we end up with a stack of `laravel-YYYY-MM-DD.log` files and a text editor. Which works  until the error you care about is buried under five thousand identical “missing token” lines. That’s the gap this tool fills.

---

## Decision · Which tool fits

### When to reach for which.

| For most projects | For restricted projects |
|---|---|
| **Sentry, Flare, or similar** | **This Log Reader** |
| Real-time alerts, release tracking, breadcrumbs, source maps, assignment workflows. If the client allows outbound telemetry, this is always the better answer. | One HTML file. No server, no network, no upload. Open in a browser, drop the log file in, read. |
| Roughly **80%** of engagements fit here. | For cases where security policy forbids sending application data off the box  and Notepad++ isn’t enough. |

---

## Scenarios

### When this earns its keep.

| # | Scenario | Why it matters |
|---:|---|---|
| **01** | **Regulated client, no outbound telemetry allowed** | Healthcare, finance, public sector. The information-security policy explicitly forbids services like Sentry. You SSH in, download the log, and need to read it on your machine, offline, without uploading anywhere. |
| **02** | **A client just sent you a single log file** | They forwarded an attachment over email. You don’t want to spin up a full ELK stack to read one day’s worth of errors. Open this, drop the file, done. |
| **03** | **Production triage on an unfamiliar server** | You’re on a customer call, they share their screen, and you need to navigate hundreds of repetitive entries to find the one that matters. Grouping collapses the noise. |
| **04** | **After the fact, when Sentry caught nothing** | An error happened before the SDK initialized, or in a CLI job that wasn’t instrumented. The log file caught it. Read it properly instead of squinting at `tail -f`. |

---

## Usage

### Three things to know.

### Step 01  Open the file. Drop the logs in.

Double-click `log-reader.html` or serve the project from any folder. Drag one or more `laravel-YYYY-MM-DD.log` files anywhere on the page, or click **Open file**.

Nothing is uploaded. Everything is parsed in your browser, in memory, and discarded when you close the tab.

---

### Step 02  Identical errors collapse into groups.

Twenty-three identical “JWT missing token” lines become one entry with a count badge. A dozen `rename(D:\…\xyz.tmp)` failures with different filenames share a group because the signature normalizes paths and IDs.

Click a headline to expand the individual occurrences. Click again to collapse.

```text
INFO   JWT error: missing_token  Authentication failed       23 occurrences   May 12 · 00:07–09:42
ERROR  rename(D:\Laravel\storage\framework\views\…tmp): Zugriff verweigert   13 occurrences   May 12 · 00:51–18:33
ERROR  The file "D:\Laravel\public" does not exist            11 occurrences   May 12 · 03:14–21:08
```

---

### Step 03  Search across messages and stack traces.

Press `/` to focus the search. Plain-text matches work anywhere: message, exception summary, or stack frame. Wrap in slashes for a regex.

```regex
/rename\(.*\.tmp/i
```

The sidebar shows totals per level, hour-by-hour activity, and the list of loaded files. Click any level to filter.

---

## At a glance

### What it is, in numbers.

| Metric | Value |
|---|---:|
| Network calls | **0** |
| HTML files | **1** |
| Total size | **45 KB** |
| Browser-side processing | **100%** |

---

## Setup

### Drop in, or unpack.

**Single file.** Save `log-reader.html` anywhere on disk. Double-click it. Done. Recommended for casual use.

---

## Honest limitations

### What this is not.

**It isn’t real-time.** You’re reading a file. If a new error happens after you opened it, reload the file.

**It isn’t a replacement for proper observability.** If your client allows Sentry or Flare, use that. Alerts, release tracking, assignment, breadcrumbs, source maps  none of that is here, and none of that should be reinvented in a single HTML file.

**It only knows Laravel’s default format.** It expects `[YYYY-MM-DD HH:MM:SS] env.LEVEL: message` headers, with Laravel’s `{"exception":…}` payload and `[stacktrace]` blocks. If you’ve customized the log formatter, the parser may need adjusting.

---

<p align="center"><strong>Log Reader</strong><br><sub>Knightify FlexCo</sub></p>
