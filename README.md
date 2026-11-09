# Zero to Specialist: .NET Performance Optimization

**A complete, step-by-step, line-by-line guide to making ASP.NET Core Web APIs and MVC applications fast — starting from absolute zero.**

Target: **.NET 10 (current LTS)** · ASP.NET Core **Web API + MVC** · EF Core + **SQL Server** · Visual Studio 2022/2026

---

## About this book

This book teaches you one skill: **making .NET web applications fast, and proving it with numbers.** It assumes you know nothing about performance work. Every concept is introduced from zero, every line of code is explained, and every chapter builds on the one before it.

We learn by doing. In Chapter 4 we build **ShopX**, a small e-commerce application (a REST API + an MVC storefront) that works — but is written the way most of us write our first applications: it gets slower the more you look at it. Every chapter after that measures one weakness, explains it from the inside out, fixes it, and measures again. By Chapter 30, ShopX is roughly **20× faster** than its baseline, and — more importantly — you can do the same to any application you are paid to work on.

## Who this book is for

- Developers who can write (or follow) basic C# and want to become the performance specialist on their team.
- Juniors who want a structured path: **Chapter 3 recaps the vocabulary, Chapter 4 explains `public`, `class`, and `virtual` word by word** — no prior deep C# knowledge is assumed.
- Mid-level/senior developers who "know some of this" but want the complete, ordered system: from HTTP cache headers to GC modes, from `Span<T>` to `HybridCache`, from `SET STATISTICS IO` to OpenTelemetry.

If you have never written a line of C#, start with the C# language tour linked in Appendix C, then return — everything else is explained on first use.

## How to read this book

1. **Type the code.** Do not copy-paste. The explanations are keyed to lines you will have written yourself.
2. **Keep a lab notebook.** After every measurement, write down what you changed and what the numbers said. A template is provided in Chapter 3.
3. **Do the exercises.** Each chapter ends with exercises (solutions included), interview questions, and a checklist.
4. **One chapter at a time in order.** Later chapters assume the code and concepts of earlier ones.

## What you will be able to do when you finish

- Profile a .NET app with Visual Studio, `dotnet-trace`, `dotnet-counters`, `dotnet-gcdump` and BenchmarkDotNet — and know *which tool for which symptom*.
- Load-test with k6, read p95/p99 correctly, and avoid the classic load-testing mistakes.
- Optimize the full request pipeline: middleware order, output/response caching, compression, JSON serialization.
- Write async code that never starves the thread pool, and recognize every async anti-pattern on sight.
- Tune EF Core + SQL Server: kill N+1 queries, use projections, covering indexes, keyset pagination, and read execution plans.
- Design a caching strategy (browser → CDN → output cache → `IMemoryCache`/`HybridCache` → distributed) with correct invalidation and stampede protection.
- Reason about memory: allocation rate, GC modes (Workstation/Server/DATAS), `Span<T>`, `ArrayPool`, and container limits.
- Ship with confidence: runtimeconfig knobs, Kestrel limits, health checks, OpenTelemetry metrics, and alerts on p95.
- Run a systematic **10-step performance audit** of any codebase — the capstone of the book.

## Requirements (set up once, ~30 minutes)

| Tool | What for | Where |
|---|---|---|
| Visual Studio 2022 (17.14+) or Visual Studio 2026, any edition incl. **Community (free)** | Writing and profiling the code | visualstudio.microsoft.com |
| **.NET 10 SDK** (select it in the VS installer) | Building and running | Included in the VS workload, or dotnet.microsoft.com |
| **SQL Server LocalDB** (installs with the "data storage and processing" workload) | The database | Ships with Visual Studio |
| **k6** (load testing) | Chapters 5, 22, 28 | k6.io — see Chapter 5 |
| **dotnet perf tools** (`dotnet-counters`, `dotnet-trace`, `dotnet-gcdump`, `dotnet-dump`) | Chapters 5, 6, 23, 29 | One command each, Chapter 5 |

Everything runs on Windows with LocalDB; the code itself is cross-platform — if you use macOS/Linux with Visual Studio Code, swap LocalDB for a SQL Server container and the menu paths for `dotnet` CLI commands (Appendix A shows both).

## A word about the numbers in this book (important)

Every chapter ends with a "scoreboard" showing ShopX's metrics before and after that chapter's change. Those tables are **illustrative runs from my development machine**. Your absolute numbers *will* differ — different CPU, different disk, different seed data. That is expected and fine: **what transfers is the method and the relative improvement, not the milliseconds.** The book repeatedly tells you to record *your own* numbers in your lab notebook — that habit, not my numbers, is what makes you a specialist.

## The chapter recipe

Every chapter follows the same shape, so you always know where you are:

1. **What this chapter is about** — plain-English introduction.
2. **A short history** — where the technique came from (and why old tutorials may teach it differently).
3. **What it provides you** — the concrete capability you gain.
4. **Why you should use it** — the cost of not using it.
5. **Implementation** — numbered sub-chapters (`x.1`, `x.2`, …) with code, explained **line by line**; the first time any C# construct appears, it is dissected **word by word**.
6. **Measuring the change** — the scoreboard delta, recorded in the lab notebook.
7. **Key takeaways → Exercises (+solutions) → Interview questions → Performance checklist.**

## Table of contents

- [Part 0: Getting Oriented](#part-0-getting-oriented)
  - [Chapter 1: What Performance Optimization Really Is](#chapter-1-what-performance-optimization-really-is)
  - [Chapter 2: A Short History of .NET and Its Performance Story](#chapter-2-a-short-history-of-net-and-its-performance-story)
  - [Chapter 3: The Language of Speed](#chapter-3-the-language-of-speed)
- [Part I: The Laboratory](#part-i-the-laboratory)
  - [Chapter 4: Building ShopX, Our E-Commerce Lab](#chapter-4-building-shopx-our-e-commerce-lab)
  - [Chapter 5: The Baseline, Measuring Before Touching Anything](#chapter-5-the-baseline-measuring-before-touching-anything)
  - [Chapter 6: The Profiler's Toolkit](#chapter-6-the-profilers-toolkit)
- [Part II: The Request Pipeline](#part-ii-the-request-pipeline)
  - [Chapter 7: Anatomy of an HTTP Request](#chapter-7-anatomy-of-an-http-request)
  - [Chapter 8: Middleware That Does Not Slow You Down](#chapter-8-middleware-that-does-not-slow-you-down)
  - [Chapter 9: Response Caching and the Browser](#chapter-9-response-caching-and-the-browser)
  - [Chapter 10: Output Caching on the Server](#chapter-10-output-caching-on-the-server)
  - [Chapter 11: Faster Payloads with Compression and System.Text.Json](#chapter-11-faster-payloads-with-compression-and-systemtextjson)
- [Part III: Asynchronous Programming](#part-iii-asynchronous-programming)
  - [Chapter 12: Threads and the Thread Pool](#chapter-12-threads-and-the-thread-pool)
  - [Chapter 13: async and await From the Inside Out](#chapter-13-async-and-await-from-the-inside-out)
  - [Chapter 14: The Async Anti-Pattern Catalog](#chapter-14-the-async-anti-pattern-catalog)
- [Part IV: Data Access with EF Core and SQL Server](#part-iv-data-access-with-ef-core-and-sql-server)
  - [Chapter 15: How EF Core Executes a Query](#chapter-15-how-ef-core-executes-a-query)
  - [Chapter 16: Query Techniques That Actually Matter](#chapter-16-query-techniques-that-actually-matter)
  - [Chapter 17: The SQL Server Side of Performance](#chapter-17-the-sql-server-side-of-performance)
  - [Chapter 18: Caching Database Results with IMemoryCache](#chapter-18-caching-database-results-with-imemorycache)
- [Part V: Caching Architecture](#part-v-caching-architecture)
  - [Chapter 19: Caching Strategy and Patterns](#chapter-19-caching-strategy-and-patterns)
  - [Chapter 20: HybridCache and the Distributed World](#chapter-20-hybridcache-and-the-distributed-world)
- [Part VI: Networking, I/O, and Resilience](#part-vi-networking-io-and-resilience)
  - [Chapter 21: HttpClient Done Right](#chapter-21-httpclient-done-right)
  - [Chapter 22: Rate Limiting to Protect the App](#chapter-22-rate-limiting-to-protect-the-app)
- [Part VII: Memory and the Runtime](#part-vii-memory-and-the-runtime)
  - [Chapter 23: Memory and the Garbage Collector](#chapter-23-memory-and-the-garbage-collector)
  - [Chapter 24: Writing Allocation-Aware Code](#chapter-24-writing-allocation-aware-code)
  - [Chapter 25: GC Modes and Runtime Knobs](#chapter-25-gc-modes-and-runtime-knobs)
- [Part VIII: MVC-Specific Optimization](#part-viii-mvc-specific-optimization)
  - [Chapter 26: Razor Views and the View Pipeline](#chapter-26-razor-views-and-the-view-pipeline)
  - [Chapter 27: Frontend Delivery for MVC Apps](#chapter-27-frontend-delivery-for-mvc-apps)
- [Part IX: Production and Mastery](#part-ix-production-and-mastery)
  - [Chapter 28: Load Testing Like an Engineer](#chapter-28-load-testing-like-an-engineer)
  - [Chapter 29: Observability with OpenTelemetry](#chapter-29-observability-with-opentelemetry)
  - [Chapter 30: The Capstone Audit and Mastery Exam](#chapter-30-the-capstone-audit-and-mastery-exam)
- [Appendices](#appendices)
  - [Appendix A: Cheat Sheets](#appendix-a-cheat-sheets)
  - [Appendix B: Glossary](#appendix-b-glossary)
  - [Appendix C: Further Reading](#appendix-c-further-reading)

---
## Part 0: Getting Oriented

You cannot optimize what you cannot define, and you cannot define what you cannot measure. Part 0 gives you the three foundations the rest of the book stands on: what this discipline actually is (Chapter 1), where .NET came from and why that history keeps saving you from mistakes (Chapter 2), and the vocabulary every measurement in the book is written in (Chapter 3).

### Chapter 1: What Performance Optimization Really Is

#### What this chapter is about

Performance optimization is the discipline of changing code, configuration, or architecture so that an application, while producing **exactly the same behavior**, consumes **fewer resources**: less time per operation, less CPU, less memory, less network, less money. It is *not* the same as refactoring (which improves structure without necessarily improving resource use), and it is the opposite of feature work (which adds behavior). In this book, every optimization must satisfy three tests:

1. **Behavior is unchanged** — same responses, same status codes, same data.
2. **A number improved** — latency, throughput, allocations, or cost, measured before and after.
3. **You know why it improved** — you can explain the mechanism, not just "I tried X and it was faster".

If any of the three is missing, you did not optimize; you changed things and got lucky (or broke something). This chapter gives you the mindset that makes the other 29 chapters mechanical instead of mystical.

#### A short history of this topic

Performance work is as old as computing, because computing itself was born scarce: in the 1960s a second of CPU time cost real money, so "efficiency engineering" was a paid job title. The discipline's most quoted sentence comes from Donald Knuth's 1974 paper *Structured Programming with go to Statements*: "premature optimization is the root of all evil" — almost always quoted without its preceding sentence: *"We should forget about small efficiencies, say about 97% of the time."* Read the whole thing once and you will never misuse it again: Knuth is not saying "don't optimize"; he is saying **don't optimize blind, at random, before measuring** — which is precisely this book's first rule.

Three waves matter for us. First, **profilers** (from the 1970s onward) moved optimization from intuition to measurement. Second, **managed runtimes** — Java in 1995, .NET in 2002 — introduced garbage collection: performance work changed from manual memory management to *allocation discipline* (Chapter 23 explains why "the GC is slow" almost always means "we allocate too much"). Third, the **web + cloud era** (2005–today) put a price tag on every millisecond: when you rent servers by the hour and users abandon slow pages in seconds, performance is a business feature, not a technical hobby. ASP.NET Core's 2016 rewrite (next chapter) was Microsoft betting the framework's future on that fact.

#### What performance optimization provides you

**For your users:** humans perceive anything under ~100 ms as instantaneous; under ~1 s as fluid. Beyond that, attention decays fast. Widely-cited industry folklore points one way (treat exact figures as legend, the direction is unanimous): in the mid-2000s Google reported that adding a few hundred milliseconds to search results measurably reduced searches per user, and Amazon engineers famously claimed ~100 ms ≈ ~1% of sales. Mobile users on poor connections feel multiples of what your dev machine feels.

**For your company:** performance is capacity. If the same server handles 5× more requests per second, you need 80% fewer servers for the same traffic. That is why a performance specialist pays for themselves: the work converts directly into infrastructure budget.

**For your product's reach:** Google uses page experience (Core Web Vitals — Chapter 27) as a ranking signal. Slow pages are literally less findable.

**For you:** performance specialists are scarce, hard to hire, and every interview loop has a perf round. The interview questions at the end of every chapter are not decoration; they are the actual questions.

#### Why you should use it

Because the alternative is the **slow spiral**: a slow app needs more servers; more servers mean more load balancers, more connection churn, more GC pressure per box; the p95 creeps up; at peak you have an incident; the hotfix (usually caching something randomly, or worse, adding threads) buys a week; the spiral continues. Teams inside the spiral ship features slower too, because every deploy is a game of roulette. Optimization, done the way this book teaches — measure, fix the top item, verify — reverses the spiral permanently. And the habit of *measuring before changing* will improve every other piece of engineering you do, because you will stop believing guesses.

#### The mindset: the four golden rules

Every chapter of this book enforces these. If you internalize only one thing today, make it this list:

1. **Measure before you change.** No measurement, no change. A guess that happens to be right teaches you nothing; a measured result teaches you the system.
2. **Optimize the hot path.** A profiler (Chapter 6) will show you that a small fraction of the code — the *hot path* — consumes most of the time (the 80/20 rule is usually 95/5 in real apps). Making cold code faster is wasted labor. Knuth's "97%" is this rule.
3. **One change at a time.** Change two things and the number improves, and you still know nothing — one change may have helped 10× and the other hurt 5×. Attribution requires isolation.
4. **Verify and record.** Re-run the exact same measurement after the change, then write it down (lab notebook, Chapter 3). Without the record, you will re-litigate the same question in three months.

A fifth rule sits above the others: **correct first, then fast.** Optimization never changes behavior — that is test 1 above. In this book ShopX's responses stay byte-comparable (modulo timing) from Chapter 4 to Chapter 30.

#### How this book works

We build one application, **ShopX** (Chapter 4): a products REST API (`ShopX.Api`) plus an MVC storefront (`ShopX.Web`) over SQL Server, with the classic sins already in it — written on purpose, because you cannot learn to fix what you have never seen. Each chapter then picks one symptom, explains the machinery underneath it from first principles, fixes ShopX, and re-measures. The running **scoreboard** (see the front-matter note about illustrative numbers) keeps us honest. When you finish, you will have personally performed, in order, every major category of .NET performance work:

| Symptom you will face someday | Part of this book that fixes it |
|---|---|
| "Responses are just slow" | Part II (pipeline, caching, compression) |
| "It falls over under load" / timeouts | Part III (async) and Part VI (HttpClient, rate limiting) |
| "The database is the bottleneck" | Part IV (EF Core, SQL Server) |
| "CPU is high / memory grows" | Part VII (GC, allocations) |
| "MVC pages render slowly in the browser" | Part VIII (Razor, frontend) |
| "It's fast on my machine, slow in production" | Part IX (load testing, observability) |

#### Measuring the change

There is no code yet, so the "measurement" of this chapter is the mindset check below. From Chapter 4 onward, every chapter's scoreboard assumes you asked these four questions before touching anything.

#### Key takeaways

- Optimization = same behavior, fewer resources, proven by a number, with a known mechanism.
- Knuth's rule, correctly read: forget small efficiencies *when unmeasured*; the hot path is where the 3% lives — find it with a profiler, not intuition.
- Performance is a business feature: user retention, infrastructure cost, SEO ranking.
- The four golden rules: measure first; hot path only; one change; verify and record.
- Correct first, then fast — behavior never changes during optimization.

#### Exercises

**Exercise 1.1 — The capacity math.** Your API currently sustains 120 requests/second on 8 servers. A specialist doubles its efficiency. How many servers do you need for the same load afterwards, and what percentage of capacity cost is saved?

*Solution.* Doubling requests/second per server means 4 servers sustain 120 rps. That is 50% of the original server count — a 50% capacity-cost reduction. (Note the trap: doubling efficiency halves servers; it does not reduce cost by 2× only — the percentage saved is 50%, and that is why "2× faster" stories are so valuable to finance.)

**Exercise 1.2 — Hot or cold?** In the ShopX API (Chapter 4 will have one), which code do you expect to be hot: the product-list endpoint or the "order placed" audit-log writer? Why?

*Solution.* The product list: every visitor hits it, continuously, on the read path. The audit writer runs once per order — orders are orders of magnitude rarer than page views. The profiler will confirm this in Chapter 6; the rule of thumb: hot paths live on high-frequency read paths; cold paths live in rare events (writes, admin, startup).

#### Interview questions

**Q: What is the first thing you do when someone says "the site is slow"?**
A: Define *slow* with a number. Which endpoint, which percentile, under what load, since when, in which environment. Then reproduce it under measurement (load test + profiler). Never start by changing code — that is the interview answer in one sentence: "measure, reproduce, isolate, then fix."

**Q: What is the difference between optimization and refactoring?**
A: Refactoring changes internal structure with no behavioral change and not necessarily any resource change; optimization changes resource consumption with no behavioral change. Both can overlap (a better algorithm is both), but the proof obligations differ: refactoring is proven by tests; optimization is proven by tests *plus measurements*.

**Q: When should you *not* optimize?**
A: When the code is not on a hot path (measured, not assumed), when the behavior is about to change anyway (features landing next sprint), when the gain is smaller than the added complexity, and always before you have a measurement. Also: never during an incident — stabilize first, measure after.

#### Performance checklist

- [ ] I can define optimization in one sentence (same behavior, fewer resources, proven)
- [ ] I know the four golden rules by heart
- [ ] I will not change code without a before-measurement
- [ ] I will change one thing at a time and re-measure
- [ ] I will keep a lab notebook
- [ ] I understand "hot path" and will find it with a profiler, not intuition
- [ ] I accept that behavior must not change while optimizing

---
### Chapter 2: A Short History of .NET and Its Performance Story

#### What this chapter is about

This chapter gives you the map of the .NET territory you are about to optimize. You need it for three practical reasons. First, **vocabulary**: in the wild, ".NET" is used for at least five different things (Framework, Core, Standard, Mono, modern .NET) and half the confusion in online answers comes from mixing eras. Second, **survival**: the internet is full of performance advice that was *correct for its era* — in classic ASP.NET, `Task.Result` could deadlock your whole request; tutorials from 2012 still teach patterns that are actively harmful in 2026. Third, **free speed**: the .NET team ships measurable runtime improvements every November; knowing the release history tells you which upgrades pay you back with zero code changes.

#### A short history of this topic

The performance story of .NET is really three eras: the Framework era (2002–2014), the Core rewrite (2014–2019), and the unified modern era (2020–today). We take them in order, then line up the release table you will refer to for the rest of the book.

##### 2.1 The Framework era (2002–2014)

**February 2002:** Microsoft ships .NET Framework 1.0 with ASP.NET **Web Forms**. Its big idea: server controls render HTML for you, and a hidden `__VIEWSTATE` field round-trips your page state on every postback. It was productive — and it taught a generation three performance habits we now spend careers unlearning: multi-kilobyte hidden fields, postbacks where a 200-byte GET would do, and "the framework decides the HTML."

The era's performance machinery was heavy by design: `System.Web` (one giant library everything depended on), an IIS-integrated pipeline (HttpModules and Handlers), and one **SynchronizationContext per ASP.NET request**. That last item is the root of the most famous .NET deadlock in history — blocking on a Task (`Task.Result`) inside a request could deadlock the request thread permanently. Chapter 13 explains the mechanism; for now, remember: **that deadlock was an ASP.NET Framework phenomenon. ASP.NET Core has no per-request SynchronizationContext**, so the same code no longer deadlocks — it does something quieter and worse (thread-pool starvation, Chapter 12). Old advice "never `.Result`, it deadlocks" is right, but for the wrong reason on modern .NET — history explains both.

Milestones worth remembering from this era: C# 2 (2005) brings **generics** — no more boxing in `List<T>`, a genuine performance leap; **ASP.NET MVC 1.0 (March 2009)** ditches Web Forms for testable controllers, clean URLs, full control of your HTML — no ViewState; **ASP.NET Web API (2012)** ships proper REST with `HttpClient`; and with .NET 4.5 / C# 5 (2012) comes **`async`/`await`** — the single biggest programming-model change in .NET history, and Parts III of this book exists because of it.

##### 2.2 The Core rewrite (2014–2019)

By 2013 the Framework had become a Windows-bound monolith: slow startup, huge memory footprint, tightly coupled to IIS and System.Web. In **November 2014** Microsoft announced the ground-up rewrite first shown as "ASP.NET 5 / Project K", and on **June 27, 2016** it shipped as **ASP.NET Core 1.0**. Everything you optimize in this book is built on the decisions of that rewrite:

- **Kestrel**, a cross-platform web server written from scratch, replaces the IIS pipeline as the hot path (IIS stays as a reverse proxy in front of it).
- A **middleware pipeline** instead of an event model — you see and pay for only the stages you add (Chapter 7).
- **Built-in dependency injection** — every service registration becomes a performance decision (scoped `DbContext` per request, Chapter 15).
- No `System.Web`, no ViewState, no per-request SynchronizationContext.

The performance effect was public and measurable: ASP.NET Core climbed the well-known TechEmpower-style open benchmarks from mid-pack to the top tier of web frameworks over the following years — a climb Microsoft openly chased, because after the rewrite, *the product itself was the runtime's performance story*. Versions 1.0→2.1 (2017–2018) delivered the runtime techniques this book uses in Part VII: tiered JIT, `Span<T>`, and `IHttpClientFactory` — introduced in 2.1 (May 2018) precisely because `new HttpClient()` was leaking sockets at scale (Chapter 21 shows the disease and the cure).

##### 2.3 The unified era (2020–2026)

.NET 5 (November 2020) merged the brands: "Core" disappeared from the runtime's name and there has been **one .NET, released every November, LTS every second year** ever since. The table below is the one you will keep referring to; the bolded items are the performance features this book uses.

| Version | Date | Support | Performance items you will use |
|---|---|---|---|
| .NET 5 | Nov 2020 | superseded by 6 | Brand unification; C# 9 records |
| .NET 6 LTS | Nov 2021 | ended Nov 2024 | **Minimal APIs**, faster startup; C# 10 |
| .NET 7 | Nov 2022 | ended May 2024 | **Rate-limiting middleware (Ch 22)**, **output caching (Ch 10)**, **EF `ExecuteUpdate`/`ExecuteDelete` (Ch 16)**, Native AOT for console apps, HTTP/3 maturing; C# 11 (`params ReadOnlySpan`, Ch 24) |
| .NET 8 LTS | Nov 2023 | ends Nov 10, 2026 | **Native AOT for minimal APIs**, **DATAS GC (Ch 25)**, **dynamic PGO on by default**, **frozen collections**, keyed DI; C# 12 collection expressions |
| .NET 9 | Nov 2024 | STS, ended May 2026 | **HybridCache (Ch 20)**, better inlining/PGO, JSON source-gen improvements; C# 13 |
| .NET 10 LTS | Nov 2025 | **until Nov 2028** | C# 14, continued JIT/GC/startup polish — **the version this book targets** |

Support status at the time of writing (November 2026): .NET 8 reaches end of support *this month*; .NET 9 ended in May 2026; **.NET 10 is the current LTS and the rational target for new work.** If you are on .NET 8 today, the upgrade to .NET 10 is usually the cheapest performance improvement available to you — teams routinely report meaningful throughput and startup gains from a version bump with zero code changes, before doing any of the work in this book.

#### What this history provides you

1. **A decoder ring for advice.** Any article older than ~2018 needs translation: "classic ASP.NET" assumptions (SynchronizationContext deadlocks, IIS pipeline, `System.Web` caching APIs, `BundleConfig` bundling) do not exist in ASP.NET Core. The modern replacements are, in order of this book: middleware (Ch 7–8), `IMemoryCache`/OutputCache (Ch 10, 18), `HybridCache` (Ch 20), WebOptimizer or build-time bundlers (Ch 27).
2. **A reason to stay current.** Every row of the table above shipped runtime speedups for free. The cheapest line in any performance plan is "upgrade the TFM".
3. **Context for architecture decisions.** Kestrel + middleware + DI are not accidents; they are the reasons your optimizations (short-circuiting, output caching, scoped services) are possible at all.

#### Why you should use it (in one paragraph)

Because in a code review, the person who can say "that pattern was correct in 2012, here is what changed in 2016, and here is the measurement proving it costs us today" is the person the team listens to. History plus a profiler beats folklore.

#### Implementation: the one line every project starts with

There is no application code yet (that is Chapter 4), but this chapter does have one line for you to type — the line every .NET project you will ever optimize begins with, and the first line we dissect word by word, as promised.

Open any `.csproj` file (right-click the project in Solution Explorer → *Edit Project File*) and find or add this element inside `<PropertyGroup>`:

```xml
<TargetFramework>net10.0</TargetFramework>
```

Line by line, word by word:

- `<TargetFramework>` — an MSBuild *property* (a named value the build reads). XML tags with PascalCase inside csproj are properties, not HTML elements.
- `Target` + `Framework` — the name: "which framework am I compiling *against*?" It decides which APIs compile, which runtime features (PGO, DATAS, AOT) are available, and which optimizations the JIT will apply when your code runs.
- `net10.0` — the **Target Framework Moniker (TFM)**: `net` = the modern .NET family; `10.0` = the version. Variants you will meet: `net10.0-windows` (adds Windows desktop APIs), `netstandard2.0` (the compatibility contract libraries used to target before unification), `net48` (Framework 4.8 — the legacy moniker; Appendix C's "further reading" includes the migration guide if your day job still lives there).

Changing this one line and rebuilding is what "upgrading the TFM" means. In Chapter 4 it will be generated for you by Visual Studio when we create ShopX.

#### Measuring the change

Nothing to measure yet — but note the first entry in your lab notebook: *"Baseline era established: net10.0, LTS until Nov 2028."* In Chapter 5 the notebook starts filling with real numbers.

#### Key takeaways

- Three eras: Framework (2002–2014, System.Web, per-request SynchronizationContext), Core rewrite (2014–2019, Kestrel + middleware + DI), unified modern .NET (2020–, yearly November releases, LTS every 2 years).
- Old advice often survives its reason: `.Result` no longer deadlocks on Core — it starves the thread pool instead (Chapter 12); both are still bugs.
- .NET 8 loses support Nov 2026; .NET 9 already ended; **target net10.0** — upgrading is frequently the cheapest perf win available.
- `<TargetFramework>net10.0</TargetFramework>` is the TFM line; every project in this book carries it.

#### Exercises

**Exercise 2.1 — Era classification.** For each snippet of "advice" below, say which era it belongs to and whether it is still correct on modern .NET: (a) "Put your page output cache directive at the top of the .aspx view"; (b) "Never call `.Result` or you will deadlock"; (c) "Register services in `ConfigureServices` and build the pipeline in `Configure`".

*Solution.* (a) Framework-era Web Forms/MVC (`<%@ OutputCache %>` directive): gone; the modern equivalent is the output-caching middleware and `[OutputCache]` attribute — Chapter 10. (b) Framework-era reasoning (SynchronizationContext deadlock): the deadlock is gone on Core but blocking is *worse* under load (thread starvation) — the advice survives with a new mechanism, Chapter 12/13. (c) ASP.NET Core 1.x–5 era: in .NET 6+ minimal hosting collapsed both into one `Program.cs` with `WebApplicationBuilder` — Chapter 4 uses the modern form.

**Exercise 2.2 — Support planning.** It is November 2026. Your service runs .NET 8 and handles Black Friday in two weeks. What do you do this month, and what do you schedule for Q1 2027?

*Solution.* This month: plan the migration (net8.0 → net10.0 is usually a low-difficulty bump), but do not ship a major change two weeks before peak — that violates "one change at a time" and "correct first". Ask for extended support guidance, apply security patches, and schedule the upgrade for the post-peak window, with a load test (Chapter 28) before rollout.

#### Interview questions

**Q: Explain the difference between .NET Framework, .NET Core, and modern .NET.**
A: Framework (2002) is the Windows-only, System.Web-coupled runtime still shipping as 4.8.x; Core (2016) is the cross-platform rewrite; modern .NET (5+, since 2020) is the continuation of Core under the unified name — one runtime, one BCL, released every November, LTS every two years. "Core" survives only in package names and old habits.

**Q: What is Kestrel and why does it matter for performance?**
A: Kestrel is ASP.NET Core's built-in, cross-platform HTTP server, written from scratch for the 2016 release. It is the code that actually owns the socket, parses HTTP, and hands requests to your middleware pipeline (Chapter 7). Before it, the hot path belonged to IIS/`System.Web` plumbing you could not see or tune. IIS/Nginx typically still sit in front of it as reverse proxies, but the request path you optimize runs inside Kestrel.

**Q: Why can old performance advice be wrong on modern .NET?**
A: Because two decades changed the machinery: no per-request SynchronizationContext (async behavior changed), a rewritten server and pipeline, a new GC with modes (DATAS), JIT features (tiering, dynamic PGO) that did not exist, and APIs that replaced old ones (`IHttpClientFactory` vs raw `HttpClient`, `HybridCache` vs hand-rolled caching). Correct advice is era-specific; measurement is era-independent.

#### Performance checklist

- [ ] I can name the three eras and one defining perf fact for each
- [ ] I know what changed in June 2016 (ASP.NET Core 1.0) and November 2020 (unification)
- [ ] I know which .NET versions are supported today and which this book targets (net10.0)
- [ ] I can explain the TFM line in my own words
- [ ] I flag any performance advice older than ~2018 for era-checking
- [ ] I have "upgrade the TFM" on my list of cheap wins for any codebase I audit

---
### Chapter 3: The Language of Speed

#### What this chapter is about

Every measurement in the rest of this book is written in a small, precise vocabulary: latency, throughput, percentiles, concurrency, allocations. Misusing these words is not pedantry — saying "the API averages 300 ms" when the truth is "p50 90 ms, p99 3 s" has shipped wrong hardware and hid real outages. This chapter teaches the vocabulary from zero, then gives you the **lab notebook method** that turns vocabulary into engineering discipline. It is short on code and long on concepts you will use in all 27 remaining chapters.

#### A short history of this topic

The math behind web performance is queueing theory, from Agner Krarup Erlang's 1909 work on telephone exchanges (how many operators does a switchboard need before callers hang up?) — the same mathematics, one century later, for web servers. **Little's Law** (John D. C. Little, 1961) is the one result you need; it links the three numbers every capacity conversation uses. Percentiles entered software performance through industrial engineering, and became non-negotiable in web ops during the 2010s, when large shops published what every SRE now repeats: in a system serving millions of requests, **the average is a fiction** — the tail is where your users live. Gil Tene's talks on "coordinated omission" (Chapter 28) completed the lesson: even your load-test tool can lie to you if you measure naively.

#### What this vocabulary provides you

1. The ability to **set targets** ("p95 under 250 ms at 50 concurrent users") instead of moods ("feels snappier").
2. The ability to **read any profiler or dashboard** in Part IX — they all speak these words.
3. The ability to **reason about capacity** with Little's Law — how much load one server can take before you need another.

#### Why you should use it

Because every optimization trade-off is expressed in it. Compression (Chapter 11) trades CPU for bytes; caching (Part V) trades memory for latency; Server GC (Chapter 25) trades RAM for throughput. If you cannot state a trade-off in these units, you cannot make it deliberately.

##### 3.1 Latency: the time of one operation

**Latency** is the time one operation takes, end to end. For a web request, user-perceived latency includes DNS, TCP connect, TLS, the request flight, **server processing time**, the response flight, and (for pages) rendering. You mostly control server processing time, but you will also meet **TTFB — time to first byte**: the moment from request start until the first response byte arrives; for APIs it is nearly the whole story, and it is the number browsers report.

Server processing time itself splits into **queueing** (time waiting for a available thread — Part III), **execution** (CPU and I/O), and **serialization** (turning your objects into bytes — Chapter 11). When you profile, you are attributing latency to these buckets. Write them down when you report a latency: "470 ms total: ~300 ms database (22 queries), ~85 ms JSON, rest framework" — that sentence is what Chapter 6's profiler will hand you.

Units: milliseconds (ms) for user-facing work; microseconds (µs) for in-process operations (a method call, a cache hit); nanoseconds are BenchmarkDotNet territory (Chapter 5). Handy magnitude rules of thumb (order-of-scale only — measure your own system): an in-process cache hit is single-digit µs; a same-region network round trip is single-digit ms; a cold LocalDB query with lazy loading, as you will see, is tens of ms.

##### 3.2 Throughput and Little's Law

**Throughput** is operations completed per unit time — requests/second (rps) for web apps. Latency is about *one* user; throughput is about *all* users. They are linked by **Little's Law**:

> **L = λ × W** — average things in the system (L) equals arrival rate (λ) times average time in system (W).

Two practical readings:

- **Capacity planning:** if each request takes W = 0.2 s and you want to serve λ = 250 rps, then L = 50 requests must be *in flight* simultaneously — you need 50 concurrent workers (threads or async continuations) that can actually run. This is why async (Part III) and thread-pool health (Chapter 12) are throughput topics, not just latency topics.
- **The optimization lever:** at *fixed* concurrency, reducing W raises possible λ. Cut average latency 5× and the same server does 5× the rps. That is the entire business case of this book in one formula.

##### 3.3 Percentiles: why the average lies

Suppose 100 requests take: 90 of them 80 ms, 9 of them 900 ms, 1 of them 4,000 ms. The **average** is (90×80 + 9×900 + 1×4000)/100 = 197 ms — looks fine on a dashboard. But one user in ten waited 900 ms, and one waited 4 s. Sort the durations and pick by position:

- **p50 (median)**: half the requests were faster than this — 80 ms here.
- **p95**: 95% were faster — 900 ms here.
- **p99**: 99% were faster — 4,000 ms here.

At 1 million requests/day, p99 is the experience of **10,000 real users** — every day. That is why this book always reports p50/p95/p99 and never trusts an average. Percentile targets are also how you'll set k6 thresholds in Chapter 5 (`p(95)<250`) and alerts in Chapter 29.

##### 3.4 Concurrency vs parallelism; synchronous vs asynchronous

Four words, four meanings, constantly conflated in interviews:

- **Concurrency** — multiple operations in flight at once; they may share one core by taking turns while waiting on I/O. A web server is concurrency-bound: its work is mostly *waiting* (database, network).
- **Parallelism** — multiple operations *executing* on separate CPU cores at the same instant. This is what `Parallel.ForEach` (CPU-bound batch work, Chapter 24) does.
- **Synchronous** — an operation blocks its caller until it completes; the blocked thread cannot do anything else (Chapter 12 shows what this costs under load).
- **Asynchronous** — an operation releases its caller while waiting; the thread serves other work and resumes when the wait completes (`async`/`await`, Part III).

A typical API is 95% waiting and 5% CPU, so *concurrency with async I/O* is the load-carrying model (Parts II–III), while *parallelism* matters only in the CPU hot spots you will find with a profiler (Part VII).

##### 3.5 Resource costs: CPU, memory, network

Latency and throughput are the *outcomes*; these are the *inputs* you spend:

- **CPU** — seconds of compute; watched as a percentage; bought as cores. High CPU with low throughput means the CPU hot path needs Part VII work (or better algorithms).
- **Memory/allocations** — every `new` (Chapter 23) costs future GC time; we track **allocations per request** (KB) as a leading indicator of tail latency. ShopX's baseline will be ~215 KB/request; we will get it under 30 KB without losing a single feature.
- **Network** — bytes on the wire (payload size); the reason compression (Chapter 11) and projections (Chapter 16) exist.

##### 3.6 The lab notebook

From Chapter 5 onward, every experiment gets one entry. Copy this template into a `LAB.md` file next to your solution and fill it religiously — it is the single habit that separates specialists from tourists:

```markdown
## E-## — <short title>            (date)
Hypothesis: <what you expect and why>
Change:      <the ONE thing you changed>
Environment: <machine, .NET version, data size, load profile>
Before:      p50=…, p95=…, p99=…, rps=…, alloc/req=…, queries=…
After:       p50=…, p95=…, p99=…, rps=…, alloc/req=…, queries=…
Verdict:     keep | revert | investigate
Mechanism:   <why, in one sentence — this line is the actual learning>
```

The `Mechanism` line is the point: six months later it is the difference between knowledge and superstition.

#### Measuring the change

No code changed in Part 0, so the "measurement" is our contract for Part I. We are about to build ShopX (Chapter 4) and measure its baseline (Chapter 5) against these targets, set now:

| Metric (at 50 concurrent users) | Target by Chapter 30 |
|---|---|
| `GET /api/products?page=1` p95 | < 100 ms (baseline will be ~470 ms) |
| Error rate under load | < 0.1% |
| EF queries per product-list request | ≤ 1 (baseline will be 22) |
| Allocations per request | < 40 KB (baseline ~215 KB) |
| `GET /catalog?page=1` (MVC) p95 | < 100 ms (baseline ~540 ms) |

#### Key takeaways

- Latency = time of one operation (report TTFB and split it into queue/execute/serialize); throughput = operations per second; Little's Law L = λ×W links them and is the capacity-planning formula.
- Never trust an average; report p50/p95/p99. p99 is 10,000 users a day at a million requests.
- Concurrency (in-flight, waiting) ≠ parallelism (cores, executing); async I/O is the load model of web apps.
- CPU, allocations per request, and payload bytes are the currencies you spend to buy latency.
- The lab notebook (hypothesis → one change → before/after → mechanism) is the discipline that makes you a specialist.

#### Exercises

**Exercise 3.1 — Percentile surgery.** A day serves these request durations: 60% at 50 ms, 30% at 200 ms, 8% at 1,200 ms, 2% at 6,000 ms. Compute average, p50, p95, p99. Which number would you show management, and which to the SRE team?

*Solution.* Sorted conceptually: p50 = 50 ms (the 60% bucket covers the median). p95 sits inside the 200 ms bucket? Cumulative: 50 ms covers ≤60th percentile; 200 ms covers ≤90th; so p95 = 1,200 ms; p99 = 6,000 ms. Average = 0.6×50 + 0.3×200 + 0.08×1200 + 0.02×6000 = 30 + 60 + 96 + 120 = 306 ms. Management: the average (306 ms) with the p95 story attached — averages communicate simply, but disclose the tail. SREs: p95 and p99 with the error budget (Chapter 29). Never the average alone: it hides that 1 in 50 users waits 6 seconds.

**Exercise 3.2 — Little's Law sizing.** You expect peak λ = 400 rps. Current average server latency is W = 0.5 s. (a) How many requests are in flight at peak? (b) A specialist cuts W to 0.1 s. What is the new in-flight count at the same 400 rps — and what did that buy you besides numbers?

*Solution.* (a) L = λ×W = 400×0.5 = 200 in flight — that needs 200 concurrent execution slots (threads or async continuations; Chapter 12 shows threads alone cannot cheaply do this). (b) L = 400×0.1 = 40. What it bought: headroom — the same server can now absorb a spike to 2,000 rps at the same in-flight pressure, or you can serve the same load with fewer/smaller instances. Latency work *is* capacity work.

#### Interview questions

**Q: Why are percentiles preferred over averages for latency?**
A: Because latency distributions are long-tailed and the tail is where users and SLAs live; the average mathematically hides the tail (a few 5 s outliers barely move a 200 ms average but wreck p99). p95/p99 also map directly to alerting thresholds and error budgets.

**Q: Explain Little's Law and one way you've used it.**
A: L = λ×W: things in system = arrival rate × time in system. Use it to size concurrency (threads/async slots) for target rps, to sanity-check dashboards (queue length should match λ×W), and to frame optimization: at fixed concurrency, cutting W multiplies sustainable λ.

**Q: Concurrency vs parallelism — one sentence each, and which one is a web API about?**
A: Concurrency = multiple in-flight operations taking turns (I/O waiting); parallelism = simultaneous execution on multiple cores (CPU work). A web API is dominated by waiting, so it is a concurrency problem — solved with async I/O — with parallelism only inside measured CPU hot spots.

#### Performance checklist

- [ ] I can define latency, TTFB, throughput, p50/p95/p99 without notes
- [ ] I can compute a percentile from a distribution and explain why averages lie
- [ ] I can apply Little's Law to size concurrency for a target rps
- [ ] I know concurrency ≠ parallelism and sync vs async, and can give an example of each
- [ ] I have created `LAB.md` from the template
- [ ] I have written down the Part I targets table

---
## Part I: The Laboratory

Optimization without measurement is superstition. Part I builds the laboratory: the application (Chapter 4), the measurement tooling and the baseline numbers (Chapter 5), and the profilers that will find our hot paths for the rest of the book (Chapter 6).

### Chapter 4: Building ShopX, Our E-Commerce Lab

#### What this chapter is about

In this chapter you create the complete application we will optimize for the next 26 chapters: **ShopX** — a products REST API (`ShopX.Api`) and an MVC storefront (`ShopX.Web`) sharing a class library (`ShopX.Data`) over SQL Server LocalDB. You will write every file yourself, and every genuinely new C# construct will be dissected word by word, as promised. The chapter ends with an honest list of the performance sins we planted on purpose — they are the curriculum of Part II onward.

#### A short history of this topic

E-commerce is the classic teaching domain for performance because its read path (browse → detail → cart) is exactly the shape of most web load: thousands of reads, rare writes. That is also why the ORMs that serve it matter here. Entity Framework (2008, .NET Framework 3.5 SP1) made databases approachable but earned a reputation for slowness that followed it into **EF Core** (first stable in June 2016, rewritten from scratch alongside ASP.NET Core). The reputation was mostly *how people used it*: the defaults optimize developer productivity (change tracking, lazy loading, full-entity materialization), and the defaults of a tutorial app survive into production. That is our plan exactly: ShopX ships with the productive defaults, and Part IV will show what each one costs and how to pay only for what you use.

#### What this chapter provides you

- A three-project solution that runs on your machine with one database.
- The C# reading skills for the rest of the book: classes, properties, constructors, attributes, DI, controllers, async action methods, Razor — each explained at first appearance.
- A seeded database (20 categories, 20,000 products) with a first embedded performance lesson: the difference between naive and bulk inserts.

#### Why you should build it (not skim it)

Every later chapter edits files you will have typed. If you skip, the line-by-line explanations stop matching anything on your disk and the book becomes theory. Budget two focused evenings; the payoff is 26 chapters of muscle memory.

##### 4.1 What we are building

```
  Browser
    │
    ├──► ShopX.Web   (MVC storefront,   http://localhost:5180)  ─┐
    │        Razor views, cookies, static files                   │
    │                                                            ├──► ShopX.Data  (entities + DbContext)
    └──► ShopX.Api   (REST API,      http://localhost:5080)  ───┘        │
         JSON over HTTP, also serves /api/mock/reviews                    ▼
                                                              SQL Server LocalDB
                                                               database: ShopX
```

Decisions, stated openly (several are *deliberate rookie sins*, fixed in later chapters):

| Decision | Where fixed | Why it's in the baseline |
|---|---|---|
| Lazy-loading proxies ON | Ch 15–16 | The most common EF Core performance bug in real codebases |
| No `Include`, no projections | Ch 16 | Lets lazy loading do maximum damage |
| Full entities returned (no DTOs) | Ch 11/16 | Payload bloat we will measure |
| No caching of anything | Ch 9, 10, 18, 20 | Every request hits the database |
| Plain `Ok(...)` JSON defaults | Ch 11 | Reflection-based serialization |
| Plain `<link href="~/css/site.css">` | Ch 27 | No fingerprinting, no cache headers |

##### 4.2 Creating the solution (Visual Studio, step by step)

1. **File → New → Project…** → search template **"ASP.NET Core Web API"** → Next.
2. Project name `ShopX.Api`, solution name `ShopX`, location wherever you keep code → Create.
3. On the last dialog: Framework **.NET 10.0**; **check "Use controllers"**; **uncheck "Configure for HTTPS"** (one less variable in our lab; Chapter 8 shows where HSTS belongs in production); uncheck OpenAPI/Use controllers' extras as offered. Create.
4. In Solution Explorer: delete `WeatherForecast.cs` and `Controllers/WeatherForecastController.cs` (template leftovers).
5. Right-click solution → **Add → New Project…** → **"Class Library"** → name `ShopX.Data` → Create (framework .NET 10.0). Delete `Class1.cs`.
6. Right-click solution → Add → New Project → **"ASP.NET Core Web App (Model-View-Controller)"** → name `ShopX.Web` → Create (.NET 10.0, uncheck HTTPS). Keep the template's `HomeController` (its `Error` action is referenced by our exception handler), delete nothing else.
7. **References:** right-click `ShopX.Api` → Add → Project Reference… → tick `ShopX.Data`. Repeat for `ShopX.Web`.
8. **NuGet packages** (right-click `ShopX.Data` → Manage NuGet Packages → Browse → Install, latest stable):
   - `Microsoft.EntityFrameworkCore.SqlServer`
   - `Microsoft.EntityFrameworkCore.Proxies`  (this is the lazy-loading sin — installed on purpose)
9. **Ports:** for each web project: right-click → Properties → Debug → General → *Open debug launch profiles UI* → set **App URL** to `http://localhost:5080` (Api) / `http://localhost:5180` (Web), remove the https entry. Fixed ports make every later measurement reproducible.

If you prefer the CLI for any step, Appendix A maps every menu path above to its `dotnet` command.

##### 4.3 The data layer: entities, word by word

Create folder `Entities` in `ShopX.Data`. Add `Category.cs`:

```csharp
namespace ShopX.Data.Entities;                 // 1

public class Category                          // 2
{                                              // 3
    public int Id { get; set; }                // 4
    public string Name { get; set; } = "";     // 5
    public string Slug { get; set; } = "";
}                                              // 6
```

The first class of the book, dissected word by word:

- **Line 1 — `namespace ShopX.Data.Entities;`**: a namespace is a named container that avoids collisions between same-named types; the semicolon after it (file-scoped namespace, C# 10) means everything in this file belongs to it without extra indentation. `ShopX.Data.Entities` reads right-to-left in using-statements later: "entities of the Data project of the ShopX solution".
- **Line 2 — `public class Category`**: `class` declares a *reference type* — its values live on the heap and variables hold references to them (Chapter 23). `public` = visible from other projects (that is how `ShopX.Api` uses it). `Category` is the type name.
- **Line 4 — `public int Id { get; set; }`**: an *auto-property*: a field plus getter/setter generated by the compiler. `int` is a 32-bit integer. EF Core maps `Id` (by convention) to the primary key, an identity column in SQL Server.
- **Line 5 — `public string Name { get; set; } = "";`**: `string` (UTF-16 text, immutable, reference type); `= ""` initializes it to the empty string so the compiler's nullable-reference analysis (enabled by default in new projects) knows it starts non-null — no "possibly null" warnings, no accidental `NullReferenceException` in seed code.
- Lines 3/6 — braces open/close the class body.

Add `Product.cs` — the pattern continues; only the *new* words are explained:

```csharp
namespace ShopX.Data.Entities;

public class Product
{
    public int Id { get; set; }
    public string Sku { get; set; } = "";
    public string Name { get; set; } = "";
    public string Description { get; set; } = "";
    public decimal UnitPrice { get; set; }      // exact base-10 type: money is never double
    public int UnitsInStock { get; set; }
    public DateTime CreatedAtUtc { get; set; } = DateTime.UtcNow;

    public int CategoryId { get; set; }                   // FK: the join to Category
    public virtual Category? Category { get; set; }       // navigation: the object behind the FK
}
```

New words: `decimal` — base-10 floating type for money (never `double` for prices: binary floats cannot represent 0.10 exactly); `DateTime.UtcNow` — initialization expression evaluated per construction; **`CategoryId` + `Category`** — the EF Core pair: the scalar foreign key and the *navigation property* to the related entity. **`virtual`** is the planted sin: it lets the lazy-loading proxy (a runtime-generated subclass) override the property's getter and *fetch the Category from the database the first time anyone reads it* — silently, per product, per request. Chapter 15 will show you the bill. `Category?` — the `?` marks the property as *nullable reference type*: the compiler enforces null-checks; with `virtual` and proxies it also means "this may not be loaded yet".

Add the remaining four entities (same grammar, nothing new except `List<T>` — a strongly-typed list, and collection navigations, also `virtual` for the same proxy reason):

```csharp
namespace ShopX.Data.Entities;

public class Cart
{
    public int Id { get; set; }
    public string OwnerId { get; set; } = "";          // the cookie value; anonymous shoppers
    public DateTime CreatedAtUtc { get; set; } = DateTime.UtcNow;
    public virtual List<CartItem> Items { get; set; } = new();
}

public class CartItem
{
    public int Id { get; set; }
    public int CartId { get; set; }
    public virtual Cart? Cart { get; set; }
    public int ProductId { get; set; }
    public virtual Product? Product { get; set; }
    public int Quantity { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public string OwnerId { get; set; } = "";
    public DateTime PlacedAtUtc { get; set; } = DateTime.UtcNow;
    public decimal Total { get; set; }
    public virtual List<OrderItem> Items { get; set; } = new();
}

public class OrderItem
{
    public int Id { get; set; }
    public int OrderId { get; set; }
    public virtual Order? Order { get; set; }
    public int ProductId { get; set; }
    public virtual Product? Product { get; set; }
    public int Quantity { get; set; }
    public decimal UnitPrice { get; set; }             // price snapshot at order time
}
```

`= new()` — target-typed `new`: the compiler infers `List<CartItem>()` from the property's type. Collection navigations **must** be initialized (the `= new()`) or EF Core cannot add to them.

##### 4.4 The DbContext and the model

Add `ShopXContext.cs`:

```csharp
using Microsoft.EntityFrameworkCore;                 // EF Core lives here

namespace ShopX.Data;

public class ShopXContext : DbContext               // 1
{
    public ShopXContext(DbContextOptions<ShopXContext> options)   // 2
        : base(options) { }

    public DbSet<Category> Categories => Set<Category>();         // 3
    public DbSet<Product> Products => Set<Product>();
    public DbSet<Cart> Carts => Set<Cart>();
    public DbSet<CartItem> CartItems => Set<CartItem>();
    public DbSet<Order> Orders => Set<Order>();
    public DbSet<OrderItem> OrderItems => Set<OrderItem>();

    protected override void OnModelCreating(ModelBuilder b)       // 4
    {
        b.Entity<Product>().HasIndex(p => p.Sku).IsUnique();      // 5
        b.Entity<Product>().HasIndex(p => p.CategoryId);
        b.Entity<CartItem>().HasIndex(ci => ci.CartId);
        b.Entity<CartItem>().HasIndex(ci => ci.ProductId);
        b.Entity<OrderItem>().HasIndex(oi => oi.OrderId);
    }
}
```

- **1 — `: DbContext`**: inheritance; `ShopXContext` *is an* EF Core context. It gets change tracking, LINQ translation, and connection management from the base class.
- **2 — the constructor**: receives `DbContextOptions<ShopXContext>` — a generics pattern (`<T>` = the type parameter; one class `DbContextOptions` works for every context type) carrying the configured provider (SQL Server, lazy proxies) and the connection string. The DI container (Section 4.6) will construct this for us per request.
- **3 — `=>` expression-bodied members**: `public DbSet<Category> Categories => Set<Category>();` is read-only sugar for `{ get { return Set<Category>(); } }`. A `DbSet<T>` is the typed gateway for querying `T`; `Set<T>()` returns it without needing one field per type.
- **4 — `OnModelCreating`**: the fluent model hook; runs once per model, where we declare things conventions cannot infer.
- **5 — `HasIndex(...)`**: creates a database index. `p => p.Sku` is a *lambda expression* — inline function: "given a `Product` p, give me `p.Sku`". EF reads the *expression tree*, not the value, so it knows which column the index covers. `.IsUnique()` — the index also enforces uniqueness (a constraint).

Note `HasIndex(p => p.CategoryId)` — that index is a *narrow* one (just the FK column). When we browse by category in Chapter 17, it will cause key lookups that a *covering* index will eliminate — our first SQL Server lesson, planted already.

##### 4.5 The seeder — our first embedded performance lesson

Add `DbInitializer.cs`:

```csharp
using ShopX.Data.Entities;

namespace ShopX.Data;

public static class DbInitializer                       // 1
{
    private static readonly string[] Categories =        // 2
    {
        "Trekking", "Camping", "Climbing", "Cycling", "Water Sports", "Winter Sports",
        "Running", "Fitness", "Fishing", "Diving", "Surfing", "Kayaking",
        "Sailing", "Travel", "Urban", "Photography", "Music", "Kitchen", "Lighting", "Tools"
    };

    private static readonly string[] Adjectives = { "Alpine", "Arctic", "Nova", "Ridge", "Compass", "Summit", "Cedar", "Fjord" };
    private static readonly string[] Nouns = { "Trekking Pole", "Lantern", "Dry Bag", "Insulated Flask", "Trail Shoes", "Headlamp", "Camp Stove", "Dry Rope" };
    private static readonly string[] Lines = { "Classic", "Pro", "XL", "Elite" };

    public static void Initialize(ShopXContext db)       // 3
    {
        db.Database.EnsureCreated();                     // 4

        if (db.Products.Any()) return;                   // 5

        var categories = Enumerable.Range(1, Categories.Length)
            .Select(i => new Category { Name = Categories[i - 1], Slug = Categories[i - 1].ToLowerInvariant().Replace(' ', '-') })
            .ToList();                                    // 6

        db.Categories.AddRange(categories);               // 7
        db.SaveChanges();                                 // 8 — one INSERT batch for 20 rows

        const int total = 20_000;                         // 9
        const int chunk = 2_000;

        for (var start = 0; start < total; start += chunk)   // 10
        {
            var products = new List<Product>(chunk);
            for (var i = start; i < start + chunk; i++)
            {
                var category = categories[i % Categories.Length];
                products.Add(new Product
                {
                    Sku = $"SKU-{i + 1:D5}",                                   // 11
                    Name = $"{Adjectives[i % 8]} {Nouns[(i / 8) % 8]} {Lines[(i / 64) % 4]} #{i + 1:D5}",
                    Description = "Built for serious use. Tested on real trails and in real weather.",
                    UnitPrice = 4.99m + (i % 496),                              // 12
                    UnitsInStock = i % 50,
                    CreatedAtUtc = DateTime.UtcNow.AddDays(-(i % 365)),
                    CategoryId = category.Id,
                });
            }
            db.Products.AddRange(products);               // 13
            db.SaveChanges();                             // 14
        }
    }
}
```

New words and the embedded lesson:

- **1 — `static class`**: cannot be instantiated; all members are shared. Utility-home pattern.
- **2 — `static readonly` arrays**: one instance for the whole app, never written after initialization — the cheapest kind of state.
- **3 — `static void Initialize`**: `void` = returns nothing.
- **4 — `EnsureCreated()`**: creates the database and tables if they do not exist (a lab convenience; real products use migrations — Appendix C). Note the lazy sin is *server-side* here too: nothing else happens until first query.
- **5 — `Any()`**: "are there any rows?" — an `EXISTS` query; our idempotence guard so re-running the app does not reseed.
- **6 — `Enumerable.Range(1, 20).Select(...).ToList()`**: LINQ over in-memory data. `Select` = "map each element through this lambda"; `i => new Category {...}` constructs from the loop index. `ToLowerInvariant()` avoids culture surprises — safe string casing (culture-correctness is Chapter 25's `InvariantGlobalization` topic).
- **9 — `const`**: compile-time constants; `20_000` underscores are readability only.
- **10 — the two nested loops**: the outer one is the *embedded performance lesson*, explained below.
- **11 — `$"SKU-{i + 1:D5}"`**: interpolation — build a string from parts at runtime; `:D5` formats as decimal, 5 digits, zero-padded (`SKU-00042`). Deterministic: same seed data on every machine, so measurements are comparable.
- **12 — `4.99m`**: the `m` suffix marks a `decimal` literal.
- **13/14 — the lesson**: each `SaveChanges()` is one transaction and one round trip to SQL Server, wrapping everything `Add`ed since the last call.

> **Embedded lesson #1 — the cost of a SaveChanges.**
> The naive alternative — one `Add` + one `SaveChanges` per product — executes **20,000 separate transactions**, each with its own round trip and log flush. On my machine that took **over 6 minutes** (illustrative). The chunked version above — 20 rows + 10 batches of 2,000 — seeds in about **8 seconds** (illustrative). Same result, 45× less wall time, same code complexity. The general rule: **batch your writes**; a `SaveChanges` is a transaction, and transactions are round trips. For serious volumes, `SqlBulkCopy` (in `Microsoft.Data.SqlClient`) streams rows even faster than chunked EF inserts — noted in Chapter 16 when we optimize the write path.

Record it in `LAB.md` as entry E-01 even though it is "just seeding": it is your first measured before/after, and the mechanism line is the learning: *round trips, not row count, dominate write cost*.

##### 4.6 Wiring the DbContext: dependency injection

Both apps register the context identically. Open `ShopX.Api/Program.cs` and make it exactly:

```csharp
using Microsoft.EntityFrameworkCore;
using ShopX.Data;
using ShopX.Data.Entities;      // used by the controllers we add next

var builder = WebApplication.CreateBuilder(args);          // 1

builder.Services.AddControllers();                        // 2

builder.Services.AddDbContext<ShopXContext>(options =>    // 3
    options.UseSqlServer(builder.Configuration.GetConnectionString("ShopX"))
           .UseLazyLoadingProxies());                     // 4

var app = builder.Build();                                // 5

using (var scope = app.Services.CreateScope())            // 6
{
    var db = scope.ServiceProvider.GetRequiredService<ShopXContext>();
    DbInitializer.Initialize(db);
}

app.MapControllers();                                     // 7
app.Run();                                                // 8
```

- **1 — `WebApplication.CreateBuilder(args)`**: the minimal-hosting model (since .NET 6). `builder` collects service registrations and configuration; `args` passes command-line arguments.
- **2 — `AddControllers()`**: registers controller discovery and the MVC pieces the API needs.
- **3 — `AddDbContext<ShopXContext>`**: registers the context in DI with **scoped** lifetime — one instance per HTTP request, disposed at the end (that per-request cost is Chapter 15 material). The `options =>` lambda configures it.
- **4 — `.UseSqlServer(...)` / `.UseLazyLoadingProxies()`**: the provider (with the connection string read from configuration — "ShopX" names the key below) and the sin, restated so you see it in code: proxies wrap entities so *any* navigation read can trigger a query.
- **5 — `builder.Build()`**: freezes the configuration into `app`.
- **6 — the seeding scope**: `using` guarantees disposal; `CreateScope()` gives a temporary service provider (a scoped `DbContext` usable during startup, before any request exists); `GetRequiredService<T>()` = "resolve T from DI, throw if missing".
- **7/8**: `MapControllers()` wires endpoint routing to controller actions; `app.Run()` starts Kestrel and blocks.

Add the connection string to `ShopX.Api/appsettings.json` (inside the existing `"ConnectionStrings"` section, or create it):

```json
{
  "ConnectionStrings": {
    "ShopX": "Server=(localdb)\\MSSQLLocalDB;Database=ShopX;Trusted_Connection=True;MultipleActiveResultSets=True"
  }
}
```

`Server=(localdb)\\MSSQLLocalDB` — LocalDB, the zero-config developer SQL Server installed with Visual Studio (`\\` is JSON escaping for one backslash). `Trusted_Connection=True` — Windows authentication. `MultipleActiveResultSets=True` — allows several active result sets per connection (lazy loading tends to need it; one more reason we will evict it in Chapter 16).

##### 4.7 The API controllers

Add `Controllers/ProductsController.cs` in `ShopX.Api` — the first controller, dissected in full:

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using ShopX.Data;
using ShopX.Data.Entities;

namespace ShopX.Api.Controllers;

[ApiController]                                          // 1
[Route("api/[controller]")]                              // 2
public class ProductsController : ControllerBase         // 3
{
    private readonly ShopXContext _db;                   // 4

    public ProductsController(ShopXContext db) => _db = db;   // 5

    [HttpGet]                                            // 6
    public async Task<IActionResult> GetList(int page = 1, int pageSize = 20)   // 7
    {
        if (pageSize is < 1 or > 100) pageSize = 20;     // 8

        var total = await _db.Products.CountAsync();      // 9
        var items = await _db.Products                   // 10
            .OrderBy(p => p.Id)
            .Skip((page - 1) * pageSize)
            .Take(pageSize)
            .ToListAsync();

        return Ok(new { total, page, pageSize, items });  // 11
    }

    [HttpGet("{id:int}")]                                // 12
    public async Task<IActionResult> GetDetail(int id)
    {
        var product = await _db.Products.FindAsync(id);  // 13
        return product is null ? NotFound() : Ok(product);
    }
}
```

- **1 — `[ApiController]`**: attribute (compiler-generated marker class) switching on API behaviors: automatic 400s for invalid models, binding of parameters from route/query.
- **2 — `[Route("api/[controller]")]`**: the URL prefix; `[controller]` is a token replaced by the class name minus "Controller" — `api/products`.
- **3 — `: ControllerBase`**: base class providing `Ok`, `NotFound`, and HTTP context without the view machinery (that would be `Controller`, used by the MVC app).
- **4/5 — constructor injection**: DI hands us a scoped `ShopXContext` when the controller is created. `readonly` — assigned once, never again. This is how *every* controller in this book receives its dependencies; the field name `_db` follows .NET convention (underscore + camelCase).
- **6 — `[HttpGet]`**: maps `GET /api/products` to this method.
- **7 — `public async Task<IActionResult>`**: read it as "runs asynchronously; when done, produces an action result". `async` + `await` get their full chapter (13); in one sentence here: *the method releases its thread while the database works*. The parameters `page`/`pageSize` bind from the query string (`?page=3`); `= 1` / `= 20` are defaults when absent.
- **8 — `is < 1 or > 100`**: pattern matching (C# 9): concise range check — clamps hostile input without a 500.
- **9 — `await _db.Products.CountAsync()`**: SQL `SELECT COUNT(*)` as `await`ed round trip. Note: **no `.AsNoTracking()`, no projection, no `Include`** — the baseline sins, by design.
- **10 — the paging query**: `OrderBy(p => p.Id)` → `ORDER BY` (stable paging requires ordering; unordered `Skip/Take` is a bug); `Skip((page-1)*pageSize).Take(pageSize)` → `OFFSET/FETCH`. One SQL statement, 22 will become 1 in Chapter 16 — see the mechanism there.
- **11 — `Ok(new { total, page, pageSize, items })`**: anonymous type — a compiler-generated class with those properties; serialized as JSON with 200 OK.
- **12 — `"{id:int}"`**: route parameter constrained to int; `GET /api/products/42`.
- **13 — `FindAsync(id)`**: EF's cache-aware find-by-key. `product is null ? NotFound() : Ok(product)` — the ternary picks the response; `is null` is the modern null test. Note we return the **whole entity**: JSON will walk into `product.Category` — the lazy proxy fires *during serialization*, once per product. That is our N+1, in the wild (Chapter 15 dissects it; the profiler will find it in Chapter 6).

Add `Controllers/CartController.cs` — the anonymous-cart pattern (cookie in, items out). New construct: `HttpContext` members via `Request`/`Response` on `ControllerBase`:

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using ShopX.Data;
using ShopX.Data.Entities;

namespace ShopX.Api.Controllers;

[ApiController]
[Route("api/[controller]")]
public class CartController : ControllerBase
{
    private readonly ShopXContext _db;
    public CartController(ShopXContext db) => _db = db;

    private string GetOwnerId()                          // 1
    {
        var ownerId = Request.Cookies["shopx_owner"];
        if (ownerId is null)
        {
            ownerId = Guid.NewGuid().ToString("n");
            Response.Cookies.Append("shopx_owner", ownerId,
                new CookieOptions { HttpOnly = true, IsEssential = true });
        }
        return ownerId;
    }

    [HttpGet]
    public async Task<IActionResult> GetCart()
    {
        var ownerId = GetOwnerId();
        var cart = await _db.Carts.FirstOrDefaultAsync(c => c.OwnerId == ownerId);   // 2

        if (cart is null)
            return Ok(new { items = Array.Empty<object>(), total = 0m });

        var items = cart.Items;                          // 3 — lazy load fires here
        var total = 0m;
        var view = new List<object>();
        foreach (var item in items)                      // 4
        {
            var product = item.Product;                  // 5 — and again, per item
            total += product.UnitPrice * item.Quantity;
            view.Add(new { item.ProductId, product.Name, product.UnitPrice, item.Quantity });
        }
        return Ok(new { items = view, total });
    }

    [HttpPost("items/{productId:int}/{quantity:int}")]
    public async Task<IActionResult> AddItem(int productId, int quantity)
    {
        if (quantity is < 1 or > 20) quantity = 1;

        var ownerId = GetOwnerId();
        var cart = await _db.Carts.FirstOrDefaultAsync(c => c.OwnerId == ownerId);
        if (cart is null)
        {
            cart = new Cart { OwnerId = ownerId };
            _db.Carts.Add(cart);
        }

        var existing = await _db.CartItems               // 6
            .FirstOrDefaultAsync(ci => ci.CartId == cart.Id && ci.ProductId == productId);

        if (existing is null)
            _db.CartItems.Add(new CartItem { CartId = cart.Id, ProductId = productId, Quantity = quantity });
        else
            existing.Quantity += quantity;

        await _db.SaveChangesAsync();
        return Ok(new { cartId = cart.Id });
    }
}
```

- **1 — `GetOwnerId()`**: reads or mints the anonymous shopper's cookie. `Guid.NewGuid().ToString("n")` — a random 128-bit id, hex, no dashes. `HttpOnly` (no JavaScript access) and `IsEssential` (exempt from GDPR consent gating) are the correct cookie flags.
- **2 — `FirstOrDefaultAsync(c => c.OwnerId == ownerId)`**: translated to `SELECT TOP(1) ... WHERE`.
- **3/5 — the deliberate lazy loads**: reading `cart.Items` triggers one query; then each `item.Product` triggers another. Three items in a cart = 1 + 3 + 3 (serialization) queries. It *works*. It is also why the cart page will be on our profiler's most-wanted list.
- **4 — `foreach`**: standard iteration; the compiler calls `GetEnumerator()`.
- **6 — `FirstOrDefaultAsync` on `CartItems`**: the upsert check; one `SaveChanges` at the end covers both insert and update paths.

Add `Controllers/OrdersController.cs` — the write path (one `SaveChanges` = one implicit transaction, so stock decrement + order insert + cart delete are atomic):

```csharp
[HttpPost("place")]
public async Task<IActionResult> Place()
{
    var ownerId = Request.Cookies["shopx_owner"];
    if (ownerId is null) return Unauthorized();

    var cart = await _db.Carts.FirstOrDefaultAsync(c => c.OwnerId == ownerId);
    if (cart is null) return BadRequest("No cart.");

    var items = cart.Items;                        // lazy load
    if (items.Count == 0) return BadRequest("Cart empty.");

    var order = new Order { OwnerId = ownerId };
    decimal total = 0;

    foreach (var item in items)
    {
        var product = item.Product;                // lazy load
        product.UnitsInStock -= item.Quantity;     // tracked entity: EF will UPDATE it
        order.Items.Add(new OrderItem
        {
            ProductId = product.Id,
            Quantity = item.Quantity,
            UnitPrice = product.UnitPrice          // snapshot the price
        });
        total += product.UnitPrice * item.Quantity;
    }
    order.Total = total;

    _db.Orders.Add(order);
    _db.Carts.Remove(cart);                        // delete cart with cascade to items
    await _db.SaveChangesAsync();                  // ONE transaction: insert+update+delete

    return Ok(new { orderId = order.Id, total });
}
```

(Include the same class header `[ApiController]/[Route("api/[controller]")]` and constructor as the other controllers.)

Finally `Controllers/MockReviewsController.cs` — a fake third-party reviews service (fixed 80 ms latency, deterministic content), which Parts III and VI will call exactly like a real external dependency:

```csharp
[ApiController]
[Route("api/mock")]
public class MockReviewsController : ControllerBase
{
    private static readonly string[] Authors = { "Aida", "Bilal", "Chen", "Dana", "Ehab", "Farida" };
    private static readonly string[] Texts = { "Solid build.", "Fast shipping.", "Does the job.", "Would buy again.", "Good value." };

    [HttpGet("reviews/{productId:int}")]
    public async Task<IActionResult> GetReviews(int productId, CancellationToken ct)   // 1
    {
        await Task.Delay(80, ct);                   // 2 — the fake slow downstream
        var count = 1 + productId % 3;

        var reviews = Enumerable.Range(1, count).Select(i => new
        {
            Author = Authors[(productId + i) % Authors.Length],
            Text = Texts[(productId * 3 + i) % Texts.Length],
            Stars = 3 + (productId + i) % 3
        });

        return Ok(reviews);
    }
}
```

- **1 — `CancellationToken ct`**: binds automatically to the client's disconnect; the third pillar of async correctness (Chapter 14).
- **2 — `Task.Delay(80, ct)`**: *asynchronous* wait — no thread is consumed. Compare with `Thread.Sleep(80)` in Chapter 12: same milliseconds, opposite economics.

##### 4.8 The MVC application

In `ShopX.Web`, make `Program.cs`:

```csharp
using Microsoft.EntityFrameworkCore;
using ShopX.Data;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllersWithViews();               // 1 — MVC: controllers + views + tag helpers
builder.Services.AddDbContext<ShopXContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("ShopX"))
           .UseLazyLoadingProxies());

var app = builder.Build();

using (var scope = app.Services.CreateScope())
{
    var db = scope.ServiceProvider.GetRequiredService<ShopXContext>();
    DbInitializer.Initialize(db);
}

if (!app.Environment.IsDevelopment())                     // 2
{
    app.UseExceptionHandler("/Home/Error");
    app.UseHsts();
}

app.UseStaticFiles();                                     // 3
app.UseRouting();                                         // 4
app.UseAuthorization();

app.MapControllerRoute(                                   // 5
    name: "default",
    pattern: "{controller=Catalog}/{action=Index}/{id?}");

app.Run();
```

New items: **1** adds view machinery (Razor, tag helpers) — one word to the compiler, whole chapters to us (26–27); **2** environment-conditional error handling and HSTS (production-only, correctly placed: Chapter 8's order chart); **3** `UseStaticFiles` serves `wwwroot/` (site.css) and short-circuits for those paths; **4/5** endpoint routing with the conventional `{controller}/{action}/{id?}` pattern, defaulting to our catalog (`/` → `/catalog`). Copy the same `ConnectionStrings.ShopX` block into `ShopX.Web/appsettings.json` (both apps share the one `ShopX` database).

Add `Controllers/CatalogController.cs` (constructors and usings as before):

```csharp
public class CatalogController : Controller               // note: Controller, not ControllerBase
{
    private readonly ShopXContext _db;
    public CatalogController(ShopXContext db) => _db = db;

    public async Task<IActionResult> Index(int page = 1, int pageSize = 20)
    {
        var total = await _db.Products.CountAsync();
        var items = await _db.Products
            .OrderBy(p => p.Id)
            .Skip((page - 1) * pageSize)
            .Take(pageSize)
            .ToListAsync();

        ViewBag.TotalPages = (int)Math.Ceiling(total / (double)pageSize);   // 1
        ViewBag.CurrentPage = page;
        return View(items);                               // 2
    }

    public async Task<IActionResult> Details(int id)
    {
        var product = await _db.Products.FindAsync(id);
        return product is null ? NotFound() : View(product);
    }
}
```

- **1 — `ViewBag`**: a dynamic dictionary passed to the view. Convenient — and Chapter 26 will replace it with a typed view model (it boxes values and invites typos; a real cost we will measure).
- **2 — `View(items)`**: returns a `ViewResult` — MVC locates `Views/Catalog/Index.cshtml`, passes `items` as the model, renders HTML.

Create `Views/Catalog/Index.cshtml` — our first Razor, dissected:

```cshtml
@model List<ShopX.Data.Entities.Product>                 @* 1 *@
@{
    ViewData["Title"] = "Catalog";                       @* 2 *@
}
<h2>ShopX Catalog</h2>
<div class="grid">
@foreach (var p in Model)                                @* 3 *@
{
    <div class="card">
        <div class="sku">@p.Sku</div>
        <h3><a asp-controller="Catalog" asp-action="Details" asp-route-id="@p.Id">@p.Name</a></h3>   @* 4 *@
        <div>@p.Category?.Name</div>                     @* 5 — the lazy load, in a view *@
        <div class="price">@p.UnitPrice.ToString("C")</div>
        <div>@p.UnitsInStock in stock</div>
    </div>
}
</div>
<div class="pager">
@for (var pg = 1; pg <= (int)ViewBag.TotalPages; pg++)    @* 6 *@
{
    <a asp-action="Index" asp-route-page="@pg"
       class="@(pg == (int)ViewBag.CurrentPage ? "current" : "")">@pg</a>
}
</div>
```

- **1 — `@model`**: declares the view's typed model; `Model` in the body is that list.
- **2 — `@{ ... }`**: a Razor code block — C# inside HTML; `ViewData["Title"]` feeds the layout's `<title>`.
- **3 — `@foreach`**: C# loop emitting markup per iteration; `p.Category?.Name` inside it — see 5.
- **4 — `asp-action`, `asp-route-id`**: *tag helpers* — server-side attributes that rewrite into real URLs at render time (`/catalog/details/42`). Chapter 26 prices them.
- **5 — `@p.Category?.Name`**: the `?.` null-conditional operator: if `Category` is null (lazy proxy not loaded / missing), emit nothing instead of throwing. **This line is a per-product lazy query while the view renders.** Twenty cards = twenty extra `SELECT`s. Planted on purpose; Chapters 15–16 evict it.
- **6 — `@(...)`, `@pg`**: inline expressions; the ternary picks the `current` CSS class. `(int)` casts `ViewBag`'s dynamic value — the boxing tax we remove in Chapter 26.

Create `Views/Catalog/Details.cshtml`:

```cshtml
@model ShopX.Data.Entities.Product
@{ ViewData["Title"] = Model.Name; }
<h2>@Model.Name</h2>
<div class="sku">@Model.Sku</div>
<p>@Model.Description</p>
<p>Category: @Model.Category?.Name</p>       @* lazy load, again *@
<p class="price">@Model.UnitPrice.ToString("C")</p>
<p>@Model.UnitsInStock in stock</p>

<form asp-controller="Cart" asp-action="Add" asp-route-productId="@Model.Id" method="post">
    <button type="submit">Add to cart</button>
</form>
```

And `Controllers/CartController.cs` (MVC flavor — same cookie logic as the API's, so abbreviations noted only):

```csharp
public class CartController : Controller
{
    private readonly ShopXContext _db;
    public CartController(ShopXContext db) => _db = db;

    private string GetOwnerId() { /* identical to ShopX.Api version */ }

    [HttpGet]
    public async Task<IActionResult> Index()
    {
        var ownerId = GetOwnerId();
        var cart = await _db.Carts.FirstOrDefaultAsync(c => c.OwnerId == ownerId);
        if (cart is null) return View(new List<CartItem>());

        var items = cart.Items;                    // lazy
        ViewBag.Total = items.Sum(i => i.Product.UnitPrice * i.Quantity);   // lazy per item
        return View(items);
    }

    [HttpPost("add/{productId:int}")]
    public async Task<IActionResult> Add(int productId)
    {
        var ownerId = GetOwnerId();
        var cart = await _db.Carts.FirstOrDefaultAsync(c => c.OwnerId == ownerId);
        if (cart is null) { cart = new Cart { OwnerId = ownerId }; _db.Carts.Add(cart); }

        var existing = await _db.CartItems
            .FirstOrDefaultAsync(ci => ci.CartId == cart.Id && ci.ProductId == productId);
        if (existing is null)
            _db.CartItems.Add(new CartItem { CartId = cart.Id, ProductId = productId, Quantity = 1 });
        else
            existing.Quantity += 1;

        await _db.SaveChangesAsync();
        return RedirectToAction(nameof(Index));
    }
}
```

`Views/Cart/Index.cshtml`:

```cshtml
@model List<ShopX.Data.Entities.CartItem>
@{ ViewData["Title"] = "Your cart"; }
<h2>Your cart</h2>
@if (Model.Count == 0)
{
    <p>Your cart is empty.</p>
}
else
{
    <table class="cart">
        <thead><tr><th>Product</th><th>Price</th><th>Qty</th><th></th></tr></thead>
        <tbody>
        @foreach (var item in Model)
        {
            <tr>
                <td>@item.Product?.Name</td>          @* lazy, per row *@
                <td>@item.Product?.UnitPrice.ToString("C")</td>
                <td>@item.Quantity</td>
            </tr>
        }
        </tbody>
    </table>
    <p class="total">Total: @ViewBag.Total</p>
}
```

Open `Views/Shared/_Layout.cshtml`, keep the template's `<head>` and body chrome, and make the nav honest:

```html
<a class="brand" asp-controller="Catalog" asp-action="Index">ShopX</a>
<nav>
    <a asp-controller="Catalog" asp-action="Index">Catalog</a>
    <a asp-controller="Cart" asp-action="Index">Cart</a>
</nav>
```

…with `@RenderBody()` where the template puts it, and this link in `<head>` (the un-fingerprinted, un-cached rookie version — Chapter 27 upgrades it):

```html
<link rel="stylesheet" href="~/css/site.css" />
```

Finally, replace `wwwroot/css/site.css` with something small but real:

```css
body { font-family: system-ui, sans-serif; margin: 0; color: #1c2733; }
header { display: flex; gap: 1.5rem; align-items: center; padding: .75rem 1.25rem; border-bottom: 1px solid #e3e8ee; }
.brand { font-weight: 700; text-decoration: none; color: #1c2733; }
nav a { margin-right: .75rem; text-decoration: none; color: #2563eb; }
main { padding: 1.25rem; }
.grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(220px, 1fr)); gap: 1rem; }
.card { border: 1px solid #e3e8ee; border-radius: 8px; padding: 1rem; }
.sku { font-size: .8rem; color: #64748b; }
.price { font-weight: 700; color: #047857; }
.pager { margin-top: 1rem; display: flex; gap: .5rem; flex-wrap: wrap; }
.pager a { padding: .25rem .6rem; border: 1px solid #cbd5e1; border-radius: 6px; text-decoration: none; color: #2563eb; }
.pager a.current { background: #2563eb; color: #fff; }
.cart { border-collapse: collapse; }
.cart th, .cart td { border: 1px solid #e3e8ee; padding: .5rem .9rem; }
.total { font-size: 1.2rem; font-weight: 700; }
```

##### 4.9 First run and smoke test

1. Set `ShopX.Web` as the startup project (right-click → *Set as Startup Project*), press **F5**. First launch seeds (~8 s, then cached by `Any()` on later runs). You should see the catalog grid and pager; click a product, add it to the cart, view the cart.
2. Stop debugging. Set `ShopX.Api` as startup, **F5**, and browse to `http://localhost:5080/api/products?page=2` — JSON; then `/api/products/42`, `/api/cart`, `/api/mock/reviews/42`.

In Visual Studio's *View → SQL Server Object Explorer*, connect to `(localdb)\MSSQLLocalDB` and open *Databases → ShopX → Tables*: `dbo.Products` with 20,000 rows (right-click → *View Data*). That panel is our SQL editor for Chapter 17.

##### 4.10 Measuring the change

Nothing was optimized yet, so there is no before/after — but log the two structural facts that become Chapter 5's hypothesis list in `LAB.md`:

- E-02: *"Baseline app built. Known sins: lazy proxies, full-entity JSON, no Include, no caching, un-versioned static files."*
- Mechanism notes to verify in Chapter 5/6: product list should issue 1 + 1 + N queries (count, page, one lazy `Category` per product); the MVC card grid should issue 1 + 1 + 20.

#### Key takeaways

- ShopX = `ShopX.Data` (entities + `ShopXContext` + seeder) shared by `ShopX.Api` (REST) and `ShopX.Web` (MVC), one LocalDB database, fixed ports 5080/5180.
- The deliberate sins (lazy proxies, no projections, no caching, default JSON, default static files) are the syllabus for Parts II–VIII.
- `virtual` navigation + `UseLazyLoadingProxies()` = queries fired by *reading a property*, including during JSON serialization and Razor rendering.
- Embedded lesson #1: batch your writes — 20,000 `SaveChanges` ≈ minutes; 11 chunked ones ≈ seconds. Round trips dominate write cost, not row count.
- One `SaveChanges` = one implicit transaction (the atomic order placement).

#### Exercises

**Exercise 4.1 — Count the queries.** Without running a profiler, predict how many SQL queries `GET /api/products?page=1&pageSize=20` executes. Then predict it for the MVC catalog page. Explain both numbers.

*Solution.* API: 1 (COUNT) + 1 (page query) + 20 (lazy `Category` per product, fired by JSON serialization) = **22**. MVC: 1 + 1 + 20 (lazy `Category?.Name` per card) = **22**. Same shape, different trigger (serializer vs view). Chapter 6 will confirm with the Database profiler, Chapter 15 explains the mechanism, Chapter 16 removes 21 of them.

**Exercise 4.2 — Seeder arithmetic.** Why do we compute `i / 8 % 8` for the noun and `i / 64 % 4` for the line in the product-name generator, instead of three independent `i % n` terms?

*Solution.* Independent moduli would cycle the same combinations together and repeat names early; the divided indices (`i/8`, then `i/64`) advance more slowly, sweeping the full 8×8×4 outer-product before repeating — plus `#{i+1:D5}` guarantees global uniqueness anyway. The deeper point: deterministic, arithmetic-only generation gives identical data on every machine — reproducible measurements.

**Exercise 4.3 — The decimal decision.** A teammate suggests `double UnitPrice` "because it is faster". Reject or accept, with reasons.

*Solution.* Reject. `double` cannot represent 0.10 exactly (binary fraction), so sums drift — a correctness bug, and correctness outranks speed (Chapter 1, test 1). `decimal` arithmetic is slower per operation, but money math is nowhere near the hot path; and if it ever is, the fix is fewer operations (batching, caching), never wrong math.

#### Interview questions

**Q: What is lazy loading, and what is its performance risk?**
A: Lazy loading defers fetching a navigation property until first access — convenient, but each access can silently issue a database query. In a loop over N entities (or a serializer walking N objects) that is the N+1 pattern: 1 query for the list plus N queries for the children. The risk is invisible in dev (small data, fast LocalDB) and catastrophic in production; fix with `Include` or, better, projections (`Select`), and reserve lazy loading for rare, single-entity screens.

**Q: What does `AddDbContext` register, and with what lifetime?**
A: It registers the context (and its options) as a *scoped* service: one `DbContext` per HTTP request, disposed when the request ends. It is not thread-safe, which is exactly why the lifetime is per-request. `AddDbContextPool` (Chapter 15) reuses instances instead of constructing them per request.

**Q: Why is one `SaveChanges` called an implicit transaction?**
A: EF Core wraps all pending inserts/updates/deletes of a single `SaveChanges` in one database transaction — either every change commits or none does. Splitting one logical operation across multiple `SaveChanges` without an explicit `TransactionScope`/`BeginTransaction` breaks atomicity (partial orders, lost decrements).

#### Performance checklist

- [ ] Solution builds; `ShopX.Api` and `ShopX.Web` run on 5080/5180
- [ ] Database seeded: 20 categories, 20,000 products (SQL Server Object Explorer shows them)
- [ ] I can explain `virtual` + proxies and point at the three lines where lazy loads fire
- [ ] I predicted the 22-query shape before reading Exercise 4.1's solution
- [ ] LAB.md contains E-01 (seeding) and E-02 (baseline sins)
- [ ] I can name every deliberate sin and the chapter that will fix it

---
### Chapter 5: The Baseline, Measuring Before Touching Anything

#### What this chapter is about

Chapter 3 gave you the vocabulary; this chapter makes it operational. You will install the measurement toolchain, take ShopX's first load-test measurements, and write the baseline into your lab notebook. From here on, every chapter starts from these numbers and must beat them measurably. Remember the standing disclaimer from the front matter: **the tables in this book are illustrative runs from my machine** — your job is to reproduce the *shape* of this chapter with your own numbers.

#### A short history of this topic

Micro-benchmarking in .NET was folk art (hand-rolled `Stopwatch` loops, wrong results, GC noise) until BenchmarkDotNet (by Andrey Akinshin and Jonne Katajamaa's community, mid-2010s) made rigorous measurement a one-attribute experience. Load testing went through ab/ApacheBench, JMeter, and arrived at **k6** (Grafana Labs, written in Go with scripted scenarios in JavaScript), which dominates modern teams because thresholds can *fail a CI pipeline*. The `dotnet-*` diagnostic tools (counters, trace, dump, gcdump) shipped with .NET Core 3.0 (2019) — the same EventPipe machinery Visual Studio's profilers use, available over SSH on a production Linux box. One toolchain, dev to prod.

#### What this chapter provides you

- A working toolchain: BenchmarkDotNet, k6, `dotnet-counters`, and (next chapter) the profilers.
- The **baseline scoreboard** — the before-column of every later table.
- The habit of separating *warmup* from *steady state* (JIT compiles code on first execution; Chapter 25 goes deeper).

#### Why you should use it

Every later "optimization" is a claim. Claims need controls: same data, same load profile, same machine, one changed variable. The baseline is your control group. Teams that skip it end up arguing; teams that have it end up shipping.

##### 5.1 Install the tools

Open a terminal (Developer PowerShell from VS) and run these four commands (one per tool; `--global` puts them on your PATH):

```text
dotnet tool install --global dotnet-counters
dotnet tool install --global dotnet-trace
dotnet tool install --global dotnet-gcdump
dotnet tool install --global dotnet-dump
```

Install k6 from k6.io/download (or `choco install k6` if you use Chocolatey), then verify everything:

```text
dotnet-counters --version
k6 version
```

Finally, create the benchmarks project: solution → Add → New Project → **Console App** named `ShopX.Benchmarks` (.NET 10.0). Manage NuGet Packages → install **BenchmarkDotNet**. This project is *only* for measuring code in isolation — it never hosts the app.

##### 5.2 BenchmarkDotNet: your first micro-benchmark

Replace `Program.cs` in `ShopX.Benchmarks` with:

```csharp
using BenchmarkDotNet.Running;

var assembly = typeof(Program).Assembly;                 // 1
BenchmarkSwitcher.FromAssembly(assembly).Run(args);      // 2
```

- **1 — `typeof(Program).Assembly`**: the compiled assembly (the .dll) this code lives in; reflection (runtime type inspection) reads the benchmark classes out of it.
- **2 — `BenchmarkSwitcher.FromAssembly(...).Run(args)`**: BenchmarkDotNet's entry point; `args` passes through command-line filters so `--filter String*` runs only matching benchmarks.

Add `StringBenchmarks.cs`:

```csharp
using BenchmarkDotNet.Attributes;

namespace ShopX.Benchmarks;

[MemoryDiagnoser]                                   // 1
public class StringBenchmarks
{
    private const int N = 200;

    [Benchmark(Baseline = true)]                     // 2
    public string ConcatLoop()
    {
        var s = "";
        for (var i = 0; i < N; i++) s += i;          // 3
        return s;
    }

    [Benchmark]
    public string StringBuilderLoop()
    {
        var sb = new System.Text.StringBuilder();    // 4
        for (var i = 0; i < N; i++) sb.Append(i);
        return sb.ToString();
    }
}
```

- **1 — `[MemoryDiagnoser]`**: adds allocation columns — the single most important attribute in this book; allocations are the currency Part VII spends.
- **2 — `[Benchmark(Baseline = true)]`**: marks a method for measurement; `Baseline = true` makes the other rows report as ratios against it.
- **3 — `s += i`**: each `+=` on a string creates a *new* string (strings are immutable); N concatenations copy O(N²) characters.
- **4 — `new StringBuilder()`**: a mutable buffer that grows amortized-O(1) per append.

Run it (always Release — Debug disables optimizations and lies):

```text
dotnet run -c Release --project ShopX.Benchmarks -- --filter StringBenchmarks --job short
```

`--job short` = fewer iterations (fine for teaching; drop it for publishable numbers). Illustrative output from my machine:

```text
| Method             | Mean     | Ratio | Gen0   | Allocated |
|------------------- |---------:|------:|-------:|----------:|
| ConcatLoop         | 21.4 us  |  1.00 | 3.9662 |   24.6 KB |
| StringBuilderLoop  |  2.1 us  |  0.10 | 0.0117 |    4.4 KB |
```

How to read the table, column by column: **Mean** — average per operation, with the tool's own error/ stddev columns omitted here; **Ratio** — vs the baseline row (0.10 = 10× faster); **Gen0** — Gen0 collections *per 1000 operations* (proof the GC is being provoked); **Allocated** — heap bytes per operation. Note the honest detail: `StringBuilderLoop` still allocates 4.4 KB — that is `i.ToString()` inside the loop producing small strings, a teaser for Chapter 24's `TryFormat`. The lesson generalizes: **in hot paths, repeated string building should use a buffer; and "faster" should always be proven with both time *and* bytes.**

##### 5.3 Load testing: the k6 script

Create `loadtest.js` in a `loadtests` folder of the solution (any folder works — k6 is independent of .NET):

```js
import http from 'k6/http';                          // 1
import { sleep } from 'k6';

const BASE_API = 'http://localhost:5080';
const BASE_WEB = 'http://localhost:5180';

export const options = {                             // 2
  stages: [                                          // 3
    { duration: '30s', target: 50 },                 // ramp up to 50 VUs
    { duration: '2m', target: 50 },                  // steady state
    { duration: '15s', target: 0 },                  // ramp down
  ],
  thresholds: {                                      // 4
    http_req_failed: ['rate<0.01'],
  },
};

export default function () {                         // 5
  const page = 1 + (__ITER % 10);                    // 6
  http.get(`${BASE_API}/api/products?page=${page}`, { tags: { name: 'api-products' } });
  http.get(`${BASE_WEB}/catalog?page=${page}`,      { tags: { name: 'web-catalog' } });
  sleep(1);                                          // 7
}
```

Line by line: **1** k6 modules; **2** the exported `options` object controls the engine; **3** `stages` describe a *closed-model* ramp (fixed virtual users, VUs) — fine for a baseline; Chapter 28 explains when this model lies and what to use instead; **4** `thresholds` fail the run (non-zero exit code — CI-ready); **5** the default function is one *iteration* executed by each VU; **6** `__ITER` is the iteration counter — cycling pages 1–10 varies the working set (and, later, exercises cache keys honestly); **7** `sleep(1)` — think time. Without think time you are DDoSing your own dev box and the numbers mean nothing.

Run it against the already-started apps (start both projects without debugging, Ctrl+F5, so the debugger doesn't distort timings):

```text
k6 run loadtest.js
```

Illustrative output (mine — abbreviations explained below):

```text
     http_reqs..................: 10,412
     http_req_duration..........:
       { name: "api-products" } avg=214ms  p(90)=388ms  p(95)=471ms  max=712ms
       { name: "web-catalog"  } avg=262ms  p(90)=471ms  p(95)=540ms  max=915ms
     http_req_failed............: 0.00%
     iterations.................: 5,206
```

Reading it: `http_reqs` — total requests (two per iteration); the `tags` we attached split `http_req_duration` per endpoint — always tag your requests; `p(95)` is the headline number (Chapter 3: percentiles over averages, always); `http_req_failed` caught by the threshold. The first ~10 seconds of every run are JIT warmup — either discard them or note them; Chapter 25 shows the startup flags that shrink this window.

##### 5.4 dotnet-counters: live vitals

With the load test running, in a second terminal:

```text
dotnet-counters monitor -n ShopX.Api --counters System.Runtime,Microsoft.AspNetCore.Hosting --refresh-interval 1
```

`-n ShopX.Api` targets the process by name; the two providers give you: **CPU Usage**, **Alloc Rate** (bytes/sec of new heap), **Gen 0 GC Count** (collections per second — healthy apps stay low), **GC Heap Size**, **ThreadPool Thread Count** and **ThreadPool Queue Length** (work waiting for a thread — Chapter 12's early-warning signal; ours reads ~0–2 for now because nothing blocks yet), and **Request Rate / Current Requests** from the hosting provider. Illustrative steady-state readings during the run: CPU ~60%, Alloc Rate ~7 MB/s, Gen0 ~2.1/s, heap 85–110 MB, queue length 0–2.

Do the allocation arithmetic now — it is the kind of back-of-envelope specialists live by: 7 MB/s ÷ 33 rps ≈ **212 KB allocated per request**. Write it down; Chapter 24's scoreboard improvements are measured against exactly this number.

##### 5.5 The baseline scoreboard

Copy this into `LAB.md` as entry **E-03 — Baseline (50 VUs, steady state)**. This table is the before-column of the entire book:

| Metric — ShopX baseline | Value (illustrative) |
|---|---|
| `GET /api/products` p50 / p95 / p99 | 182 / **471** / 615 ms |
| `GET /catalog` (MVC) p50 / p95 / p99 | 231 / **540** / 720 ms |
| Throughput per endpoint (think time 1 s, 50 VUs) | ~33 rps |
| Error rate | 0.00% |
| EF queries per product-list request | 22 (predicted in Ch 4; profiler confirms in Ch 6) |
| Allocations per API request | ~215 KB |
| JSON payload, 20 products | 16.8 KB (no compression active by default) |
| HTML payload, `/catalog` (≈1,000 pager links ride along — Ch 27 will catch this) | ~86 KB |
| Peak CPU (Api) | ~60% of one busy dev box |
| ThreadPool queue length | 0–2 (nothing blocks yet) |

And the targets (from Chapter 3), now that "before" exists: p95 < 100 ms on both endpoints; ≤ 1 EF query per list request; < 40 KB allocations per request; errors < 0.1%. We will not touch code until Chapter 8, because next comes the tool that tells us *where* the 471 ms live.

#### Measuring the change

No change yet — this chapter *is* the measurement. LAB.md now holds E-01 (seeding), E-02 (sins), E-03 (baseline).

#### Key takeaways

- Toolchain installed: `dotnet-counters/trace/gcdump/dump`, k6, BenchmarkDotNet (`ShopX.Benchmarks`).
- BenchmarkDotNet rules: Release build, `[MemoryDiagnoser]`, read Mean *and* Allocated, never trust a single run.
- k6: stages for load shape, thresholds as CI gates, tags to split metrics, think time for realism; discard JIT warmup.
- dotnet-counters gives live CPU/alloc/GC/thread-pool vitals; allocation rate ÷ rps = KB per request.
- The baseline scoreboard (E-03) is the before-column of every chapter that follows.

#### Exercises

**Exercise 5.1 — Allocation arithmetic.** During a load run you read Alloc Rate = 14 MB/s at 65 rps. Estimate allocations per request, and state one reason the estimate might be off by ±20%.

*Solution.* 14,000 KB/s ÷ 65 rps ≈ 215 KB/request. Off-by factors: background allocations (GC bookkeeping, timers, logging), bursty vs smooth arrival (averaging window), and other processes on the same box if you measured machine-wide. The fix for the first is a per-request measurement (Chapter 24's `LAB.md` columns; OpenTelemetry histograms in Chapter 29).

**Exercise 5.2 — Warmup window.** Your p95 chart shows the first 90 seconds 3× worse than the rest. Name two mechanisms and one mitigation for each.

*Solution.* Mechanisms: (1) JIT tiering — first executions compile at Tier0 then recompile hot methods at Tier1+PGO (Chapter 25 discusses `TieredPGO`, ReadyToRun); (2) cold caches — EF query-plan cache, output cache empty, DB buffer pool cold, connection pool warming. Mitigations: pre-warm with a synthetic ramp (stages), publish ReadyToRun (`<PublishReadyToRun>true</PublishReadyToRun>`), and in Chapter 29 you will see production warmup traffic shifting gradually (canary percentages), not a hard switch.

#### Interview questions

**Q: Why must benchmarks run in Release?**
A: Debug builds disable JIT optimizations, insert NOPs for breakpoints, and skip inlining — results can be an order of magnitude slower and the *ratio* between approaches changes too. Release (with `[MethodImpl(MethodImplOptions.NoOptimization)]` nowhere in play) is the only honest configuration; same reason load tests never run under a debugger.

**Q: What is think time in a load test and why does it matter?**
A: A pause between iterations simulating human behavior. Without it, each VU re-requests immediately — that is a robot horde, not a user population: it inflates throughput, under-represents per-user latency, and can synchronize request waves (see "coordinated omission", Chapter 28). Realistic think times (0.5–3 s) produce defensible p95s.

**Q: k6 reports p95 per tag. Why tag requests at all?**
A: One aggregate p95 across endpoints mixes a 5 ms health check with a 400 ms report — the average hides both. Tags slice metrics per endpoint/scene so thresholds can target each (`http_req_duration{name:api-products}`), and dashboards can alert per-SLO (Chapter 29).

#### Performance checklist

- [ ] `dotnet-counters --version` and `k6 version` both answer
- [ ] `ShopX.Benchmarks` runs and I can explain every column of its table
- [ ] `loadtest.js` saved; ran green against both apps on ports 5080/5180
- [ ] I read Alloc Rate and derived KB/request myself
- [ ] E-03 baseline table copied into LAB.md with *my* numbers
- [ ] I know the targets: p95 < 100 ms, ≤ 1 query, < 40 KB/req, < 0.1% errors

---
### Chapter 6: The Profiler's Toolkit

#### What this chapter is about

The baseline says *how slow*; the profiler says *where*. This chapter walks you through the four ways to find a hot path in .NET — Visual Studio's built-in profilers (CPU, Database, Instrumentation), `dotnet-trace`, `dotnet-gcdump`/`dotnet-dump`, and (from the last chapter) `dotnet-counters` — and ends with the one table worth memorizing: **which tool for which symptom**. We then point the toolkit at ShopX and get the findings that structure the whole book.

#### A short history of this topic

Sampling profilers ride hardware interrupts to record what is executing; instrumentation profilers rewrite your methods to time themselves (accurate, but the observation changes the result). Visual Studio has shipped them since the 2000s; the revolution for .NET was **EventPipe** (.NET Core 3.0, 2019): the same event stream the runtime uses internally, exposed to any tool over any transport — which is why `dotnet-trace` can profile a container in Azure from your laptop with no code changes. PerfView (Vance Morrison's tool, free) popularized the aggregated view: **"inc" (inclusive) and "exc" (exclusive) time per method**, and the flame graph it inspired became the universal picture of where time goes.

#### What this chapter provides you

- Repeatable profiling sessions on your own machine, with menu paths and commands.
- The ability to read a call tree / flame graph without fear, and the symptom→tool decision table.
- ShopX's confirmed hotspots: the evidence base for Parts II–V.

#### Why you should use it

Because every optimization you will ever do starts as an attribution problem: "these 471 ms — who is spending them?" Guessing produces cargo cults (caching random things, "LINQ is slow" folklore). Profiling produces mechanisms.

##### 6.1 The four instruments, one mental model

| Instrument | Question it answers | Overhead | Chapter |
|---|---|---|---|
| `dotnet-counters` | Is the system healthy *right now*? (CPU, alloc rate, GC, pool) | ~none | 5 |
| Sampling profiler (VS **CPU Usage**, `dotnet-trace`) | Which methods consume CPU? | ~1–5% | this one |
| VS **Database** + **Instrumentation** tools | Where does wall-clock time go, incl. waiting on SQL? | moderate | this one |
| `dotnet-gcdump` / `dotnet-dump` | What is holding memory? | pause-level | 23 |

**Sampling** takes ~1000 snapshots/sec of every thread's stack: cheap, statistically valid for CPU-heavy paths, blind to pure waiting. **Instrumentation** times every method call: exact, but heavy — use it on a narrow repro. The Database tool timelines ADO.NET/EF calls so an 80 ms SQL wait is visible as 80 ms *of waiting*, not silence.

##### 6.2 Session one: Visual Studio CPU Usage + Database on ShopX.Api

1. Start `ShopX.Api` with **Ctrl+F5** (no debugger). Leave it running.
2. Open a terminal in the solution folder and start the load: `k6 run loadtest.js` (use the Chapter 5 script, or a copy with only the `api-products` line to keep the picture clean).
3. In Visual Studio: **Debug → Performance Profiler… (Alt+F2)**. Tick **CPU Usage** and **Database**. In *Target settings* choose the running `ShopX.Api` process (or set the project as startup and let it launch). Click **Start**, let it record ~60 s during steady load, then **Stop collection**.
4. The report opens. Switch the CPU view to **Call Tree**, and expand the hottest root path. On my machine (illustrative), the inclusive-time leaders under a request look like this, in order:

```text
Microsoft.AspNetCore.Mvc … ExecuteActionAsync            (framework plumbing, small)
  ShopX.Api.Controllers.ProductsController.GetList      (our code — surprisingly thin)
    Microsoft.EntityFrameworkCore… QueryAsync            ~38% of request CPU
      … Materialization (ChangeTracking + proxies)      the 20 lazy Categories live here
    System.Text.Json … WriteObjectAsync                 ~19% of request CPU
      reflection-based serialization of 20 full entities
```

5. Click the **Database** (or *Data*) report view: a timeline of every SQL statement with durations. This is where Chapter 4's prediction gets confirmed — count the `SELECT TOP(1)` statements after the page query: one per product, each a lazy `Category` load, ~1–3 ms each in LocalDB, *per request*. Twenty of them. That is our 22 queries (1 count + 1 page + 20 lazy), visible as a staircase on the timeline.

Reading a call tree: **Inc** (inclusive) = time in a method *including* its children; **Exc** (exclusive) = time in the method alone. A method with huge Inc and near-zero Exc is a *pass-through* — the culprit is below it; drill into the biggest child at each level (that is all flame-graph reading is, turned sideways).

##### 6.3 Session two: dotnet-trace (the production-grade path)

Same load running, second terminal:

```text
dotnet-trace collect -n ShopX.Api --profile cpu-sampling -d 30
```

`--profile cpu-sampling` selects the event providers (same machinery VS used); `-d 30` stops after 30 s. You get a `.nettrace` file — openable in Visual Studio, PerfView, or converted for the web-based speedscope viewer:

```text
dotnet-trace convert ShopX.Api_2026xxxx.nettrace --format speedscope
```

When to use this instead of VS: a customer box, a Linux container, CI — anywhere Visual Studio cannot sit. The findings are identical: our EF materialization + lazy loads and JSON serialization dominate.

##### 6.4 Session three: memory snapshots

Two tools, different questions:

```text
dotnet-gcdump collect -n ShopX.Api
dotnet-gcdump report ShopX.Api_2026xxxx.gcdump    # top types by retained size
```

A gcdump is a live-heap census (what types exist, counts, sizes, who references whom) without stopping the process for long. If the heap grows across requests, two dumps a few minutes apart show *which type* grew — Chapter 23 uses this to catch our caching mistakes before they ship. `dotnet-dump` is the heavier sibling (full process snapshot with a debugger command line: `clrstack`, `dumpheap -stat`, `gcroot`) — for post-mortem analysis of crashes and leaks; we will use it in Chapter 23.

##### 6.5 The table worth memorizing

| Symptom | First tool | Then |
|---|---|---|
| "CPU is high" | VS CPU Usage / `dotnet-trace` | BenchmarkDotNet on the extracted hot method |
| "It's slow but CPU is low" | VS Database tool / `dotnet-counters` (watch ThreadPool Queue Length — waiting) | Chapter 12 async audit |
| "Memory grows / GC pauses" | `dotnet-counters` (Alloc Rate, Gen2 count) → `dotnet-gcdump` diff | Chapter 24 allocation hunt |
| "One endpoint is slow" | k6 with tags (isolate) → profiler on that path | — |
| "Only in production" | OpenTelemetry traces (Chapter 29), `dotnet-trace` on the live process | — |

##### 6.6 ShopX's confirmed diagnosis

The profiler turns Chapter 4's sin list into an evidence-based roadmap (percentages illustrative, from the CPU report under load):

1. **~38% — EF Core materialization + lazy loading** (22 queries/request, tracked entities, proxy loads). Fix: Part IV (Ch 15–16: projections, `AsNoTracking`; Ch 17: indexes) and Part V (Ch 10, 18, 20: caching, so most requests stop reaching EF at all).
2. **~19% — System.Text.Json reflection serialization** of full entities (Category nested in every product). Fix: Chapter 11 (source generation, smaller payloads).
3. **The rest is framework plumbing** — Kestrel, routing, MVC pipes: already fast; we shave it via middleware order (Ch 8) and output caching (Ch 10), which skips whole stages per request.

Notice what is *not* on the list: nothing in Part VII (memory) yet — allocations are ~215 KB/request, which matters at scale but is not today's dominant term. The order of the book parts is the order of this evidence.

#### Measuring the change

LAB.md entry **E-04 — Profiler findings**: paste your call-tree top-5 and the Database timeline count (did you also land on 22 queries?). Everything from Chapter 7 onward exists to attack item 1 and 2 of the diagnosis.

#### Key takeaways

- Four instruments: counters (health), sampling (CPU), database/instrumentation (wall clock + SQL waits), gcdump/dump (memory).
- Inc vs Exc: pass-through methods point downward; drill the fat child.
- EventPipe tools (`dotnet-trace/counters/gcdump`) work anywhere — same findings as Visual Studio, no IDE required.
- ShopX diagnosis: ~38% EF materialization + lazy loads, ~19% JSON reflection, rest plumbing — this is the itinerary of the book.

#### Exercises

**Exercise 6.1 — Tool choice.** For each ticket, pick the first tool and say why: (a) "checkout page takes 4 s only after the marketing mailshot"; (b) "gen2 GCs every few minutes, heap 2 GB and rising"; (c) "API returns in 40 ms in the office, 900 ms for the branch office".

*Solution.* (a) Isolation first: k6 tag on checkout during a reproduced spike, then Database tool on that path — wait time (SQL, external calls) will dominate while CPU idles; check ThreadPool Queue Length for blocking (Chapter 12's signature). (b) `dotnet-counters` to confirm Alloc Rate + Gen2 trend, then `dotnet-gcdump` twice, diff by type — Chapter 23/24 workflow. (c) Latency difference by *location* is network — measure with traces (Chapter 29: OTel spans include network hops) before touching code; but check response size too — 16 KB × slow links hurts; compression (Chapter 11) is the cheap fix.

**Exercise 6.2 — Read a call tree.** Given `Inc/Exc`: `GetList 38/0 ms`, `QueryAsync 36/1 ms`, `Materialize 20/18 ms`, `WriteObjectAsync 19/0 ms`, `Serialize 18/17 ms` — where is the time, and what would you do first?

*Solution.* Two fat *leaf* methods: `Materialize` (18 ms exc — EF building tracked entities) and `Serialize` (17 ms exc — JSON reflection). `GetList`, `QueryAsync`, `WriteObjectAsync` are pass-throughs (big Inc, ~0 Exc). First move: Chapter 16's projection+`AsNoTracking` shrinks Materialize; Chapter 11's source-gen shrinks Serialize. This exact shape is why the book does data access (Part IV) before runtime knobs (Part VII).

#### Interview questions

**Q: Sampling vs instrumentation profiling — trade-offs?**
A: Sampling: statistical snapshots of stacks, ~1–5% overhead, great for CPU-heavy production-like sessions, blind to pure waits. Instrumentation: rewrites methods to record exact timings, precise (catches waits and short methods), but high overhead can distort results ("observer effect"). Rule: sample first, instrument a narrow repro second.

**Q: A method shows 40% inclusive but 0.1% exclusive. Is it a problem?**
A: No — it is a pass-through; the cost is in its callees. Drill down the biggest child; optimizing the wrapper itself (micro-"optimizing" a loop that just awaits) is a classic beginner trap. Exclusive time is where your code's own cost lives; inclusive is how you navigate to it.

**Q: How do you profile something you cannot run locally?**
A: EventPipe tools over the process: `dotnet-trace collect -p <pid> --profile cpu-sampling` (works in containers; SSH tunnel as needed), `dotnet-counters monitor` for live vitals, and in a containerized world, OpenTelemetry + a profiler agent (Chapter 29). Never ship a debug build to get better numbers.

#### Performance checklist

- [ ] I ran Alt+F2 CPU+Database on ShopX.Api during load and found the EF + JSON hotspots myself
- [ ] I can explain Inc vs Exc and read the call tree top-5
- [ ] `dotnet-trace collect` produced a nettrace I opened (VS or speedscope)
- [ ] `dotnet-gcdump collect/report` ran without error
- [ ] E-04 findings pasted into LAB.md
- [ ] I can recite the symptom→tool table from memory

---
## Part II: The Request Pipeline

Chapter 6 diagnosed ShopX: ~38% EF Core, ~19% JSON, the rest plumbing. Part II optimizes the plumbing itself — the path every request walks. First the map (Chapter 7), then the three big levers that skip work entirely: middleware order (8), HTTP/browser caching (9), server-side output caching (10), and finally the wire: compression and serialization (11).

### Chapter 7: Anatomy of an HTTP Request

#### What this chapter is about

Before you can make a request cheaper, you must know what a request *is*. This chapter walks one `GET /api/products?page=1` from TCP accept to the last response byte, naming every stage, what it costs, and which later chapter optimizes it. No code changes to ShopX — this is the map for the next four chapters.

#### A short history of this topic

In classic ASP.NET, this path was *IIS*: a Windows service owned the socket, ran the request through a fixed HttpApplication pipeline (dozens of events whether you used them or not), then handed off to your code. The 2016 rewrite (Chapter 2) replaced all of it: **Kestrel** owns the socket; a composable **middleware pipeline** replaces the fixed event chain — you pay only for the stages *you* add; **endpoint routing** (introduced 2.2, stabilized 3.0) replaced pattern-matching routes with a precomputed table of endpoints. Every performance feature in Part II — short-circuiting, output caching, compression — exists because this pipeline is *yours to shape*.

#### What it provides you

The ability to reason about "where did the milliseconds go" before any profiler runs, and the vocabulary (middleware, endpoint, binding, filters, result execution) used by every optimization from here on.

#### Why you should use it

Because most "slowness" is not in your action method — it is in stages around it, and some of those stages can be skipped entirely (the fastest code is the code that never runs — that is what caching *is*).

##### 7.1 The whole path, one diagram

```
Client
  │  TCP connect (accept queue) ————————————————— Kestrel owns the socket
  ▼
┌──────────────────────────────────────────────────────────────┐
│ KESTREL: parse HTTP/1.1|2|3 → build HttpContext              │  ~tens of µs
└──────────────────────────────────────────────────────────────┘
  ▼
MIDDLEWARE PIPELINE — in the order YOU registered (Ch 8)
   ExceptionHandler → HSTS → StaticFiles → Routing → RateLimiter
   → ResponseCaching/OutputCache → Compression → CORS → AuthN
   → AuthZ … each stage either handles the response or calls next()
  ▼
ENDPOINT ROUTING — the matched action is chosen from a table   ~sub-µs (warm)
  ▼
MODEL BINDING + validation — query/route/body → parameters     cached per shape
  ▼
FILTERS — authZ, resource, action, exception filters            ~µs each
  ▼
YOUR ACTION — ProductsController.GetList or a minimal handler   ← the only code you wrote
  ▼
RESULT EXECUTION — ObjectResult → formatter → JSON (Ch 11)
                   ViewResult → Razor engine (Ch 26)
  ▼
RESPONSE WRITE — through compression middleware, back out Kestrel
```

##### 7.2 Kestrel: the front door

Kestrel accepts the socket, parses the request into a reusable `HttpContext` (one per request, pooled buffers underneath), and dispatches it to the first middleware. Costs here are microseconds and not your problem until Part VII — but two facts matter now: **it is already async all the way down** (waiting for the next byte never blocks a thread), and transport-level options (connection limits, HTTP versions) live at `builder.WebHost.ConfigureKestrel` (Chapter 25).

##### 7.3 Middleware: your pipeline, your rules

Each `app.Use...()` registration is one stage. A stage sees the `HttpContext`, does its work, and either (a) calls `next()` — hand the request deeper — or (b) **short-circuits**: writes a response itself and never calls `next()`. `UseStaticFiles` short-circuits for `/css/site.css`; `UseResponseCaching` may answer from memory; `Map`/`UseWhen` branch. Short-circuiting is the most powerful optimization primitive in ASP.NET Core: everything after the short-circuit — binding, your action, the database — is *skipped*. The next chapter is entirely about ordering and short-circuiting these stages.

##### 7.4 Routing, binding, filters

- **Endpoint routing**: at startup, ASP.NET Core builds a table of every routable endpoint (`[Route]`s, `Map*` calls) with its pattern, constraints, and metadata. Matching is a cached, optimized lookup — sub-microsecond once warm. You will never profile this; know only that routing happens *in the middleware pipeline* (that is why `UseRouting` must precede anything that needs the endpoint, like rate limiting or authorization).
- **Model binding**: converts route/query/body data into your action parameters (`int page = 1` in Chapter 4). Reflection-based but cached per action shape; the first request per action pays a small cost — one more reason to warm up before measuring.
- **Filters**: hooks around your action (authorization, resource, action, exception). Cheap; they matter to us only because `[OutputCache]` (Chapter 10) and `[ResponseCache]` (Chapter 9) are implemented as result filters/middleware and `[EnableRateLimiting]` (Chapter 22) rides the endpoint metadata.

##### 7.5 Result execution

The return value of your action is an `IActionResult` (or, for minimal APIs, whatever the handler returns). A *formatter* serializes it — `System.Text.Json` for APIs (Chapter 11), the Razor engine for `ViewResult` (Chapter 26). This stage is where our 19% JSON cost lives; the profiler showed it as `WriteObjectAsync` under your action.

##### 7.6 Where ShopX's time actually goes

Attribution of the baseline p95 471 ms (mean 214 ms; illustrative, from Chapter 6's CPU report + Database timeline):

| Stage | Share (mean) | Fixed by |
|---|---|---|
| EF Core: count + page query + 20 lazy loads | ~60% (~128 ms) | Ch 15–17 (queries), Ch 10/18/20 (caching skips them entirely) |
| JSON reflection serialization (16.8 KB) | ~19% (~40 ms) | Ch 11 (source-gen + smaller payloads via Ch 16 projections) |
| Kestrel + routing + binding + plumbing | ~5% (~11 ms) | already fast; Ch 8 shaves ordering, Ch 25 tunes the runtime |
| Queueing / scheduling noise | remainder | Ch 12 (async health), Ch 25 (GC mode) |

The strategic insight: **the top two rows are 80% and both are avoidable per-request** — by serving from caches (Chapters 9, 10, 18, 20) the request never reaches EF; by shipping smaller payloads (Chapters 11, 16) the formatter does a fraction of the work. Part II implements exactly that.

##### 7.7 Minimal APIs vs controllers (a pipeline note)

The same pipeline serves minimal APIs (`app.MapGet("/x", () => ...)`) — the differences: no `Controller` class instantiation per request, no filter/model-binding machinery beyond simple parameter binding, and (with .NET 8+) a Native AOT path for the whole request handler (Chapter 25). Controllers buy you conventions, `ControllerBase` helpers, and view support. For hot, simple endpoints, minimal APIs are measurably lighter; ShopX keeps controllers because its API surface is conventional — a reasonable trade, now made consciously.

#### Measuring the change

No code changed; the measurement of this chapter is the attribution table in 7.6 going into `LAB.md` (E-05 — request budget). From now on, every optimization chapter states which row of this table it attacks.

#### Key takeaways

- One request = Kestrel parse → middleware chain (short-circuit possible) → endpoint routing → binding → filters → your action → result execution → response write.
- Middleware is composable and *yours*: ordering and short-circuiting are the primitive optimizations (Chapter 8).
- The profiler's findings map to stages: EF materialization is your action row; JSON is result execution; the plumbing row is already ~5% — do not cargo-cult optimizing it.
- Minimal APIs skip controller overhead and unlock AOT; controllers buy conventions and views. Choose per endpoint, consciously.

#### Exercises

**Exercise 7.1 — Stage budgeting.** You must get a 120 ms endpoint under 40 ms. Profiling: 70 ms SQL waits, 30 ms serialization, 10 ms binding/plumbing, 10 ms your code. Rank the interventions.

*Solution.* (1) Cache the result so the request short-circuits before your action — output caching kills all 120 ms on hits (Ch 10); (2) if it must be computed: shrink the SQL row (projection, index — Part IV) and the payload (source-gen + smaller DTOs — Ch 11/16); (3) last: micro-tuning plumbing (10 ms) — the classic mistake is optimizing row three first because it "feels" like engineering.

**Exercise 7.2 — Short-circuit hunt.** In ShopX, name three requests that should short-circuit as early as possible, and where.

*Solution.* `/css/site.css` and `wwwroot` assets at `UseStaticFiles` (skip routing/binding/actions); 404s for unknown paths at `UseStatusCodePages`/routing (no action execution); health checks (`/healthz`, Chapter 29) *before* heavy middleware — never cached, never rate-limited. Every short-circuit = the entire right side of the diagram never runs.

#### Interview questions

**Q: Walk me through what happens between TCP accept and your action method.**
A: Kestrel accepts and parses into `HttpContext`; the middleware chain runs in registration order (each may short-circuit); `UseRouting` resolves the endpoint; binding fills parameters; filters run; then the action. Result execution serializes the return value and the response flows back out. Knowing the order explains most caching/auth bugs ("why is my cached page showing for anonymous users?").

**Q: Why can middleware ordering be a performance feature?**
A: Because stages before a short-circuit are skipped: static files answered before routing skip the whole right side; caching before compression serves stored bytes without re-compressing; expensive auth after cheap 404s avoids crypto work on garbage traffic. Ordering is configuration, but it changes which code executes per request.

**Q: Minimal API or controller — how do you decide for a hot endpoint?**
A: Hot, simple, no conventions needed → minimal API (less per-request machinery, AOT-capable with .NET 8+). Need conventions/filters/view support/team familiarity → controllers. Measure the endpoint first; the difference is real but usually smaller than the data-access work behind it.

#### Performance checklist

- [ ] I can draw the pipeline from memory (Kestrel → middleware → routing → binding → filters → action → result)
- [ ] I can explain short-circuiting and give three examples in ShopX
- [ ] The E-05 attribution table is in my LAB.md with my numbers
- [ ] I know which chapter attacks each row (Ch 10/18/20; Ch 11/16; Ch 8/25)
- [ ] I understand why plumbing (~5%) is the *last* place to optimize

---
### Chapter 8: Middleware That Does Not Slow You Down

#### What this chapter is about

Middleware is the code *around* your code. This chapter teaches the two lever it gives you — **ordering** (which stages run before which) and **short-circuiting** (skipping everything after a stage) — plus how to write custom middleware that costs nothing per request. We also install a permanent instrument: an `X-Elapsed-Ms` response header that every later chapter uses to see server-side time at a glance.

#### A short history of this topic

The idea is old (chain-of-responsibility), but ASP.NET Core (2016) made it the *only* structure: `app.Use(...)`, `app.Map(...)`, one `HttpContext` flowing through. OWIN (2011, the open standard that preceded it) proved the shape; Katana implemented it on Framework; ASP.NET Core rebuilt the idea without OWIN's abstraction tax. The performance significance: for the first time, the request pipeline was *explicit code you read top to bottom*, and "which middleware runs first" became a deliberate engineering decision instead of framework folklore.

#### What it provides you

- A canonical order chart for every middleware this book will add (with the reasoning, so you can adapt it to any app).
- The `Use`/`Map`/`UseWhen` vocabulary and how to write zero-allocation custom middleware.
- The `X-Elapsed-Ms` header — ShopX's built-in stopwatch, used by every following chapter.

#### Why you should use it

Because ordering bugs are silent: caching *before* rate limiting caches your 429s; compression *after* static files never compresses your API; auth *before* static files burns crypto on every CSS file. Order is one of the cheapest optimizations and one of the most common production mistakes.

##### 8.1 The canonical order (the chart to keep)

By the end of the book, both ShopX apps use this order. Annotations give the reasoning — the *why* is the transferable part:

```text
1  UseExceptionHandler        catches everything below it; must be outermost-ish
2  UseHsts                    (production only) one header write per response
3  ElapsedMsMiddleware        instrumentation, outermost-but-inside-exception-handler
4  UseStaticFiles             short-circuits /css/site.css etc. — before anything expensive
5  UseRouting                 resolves the endpoint — must precede anything endpoint-aware
6  UseRateLimiter             (Ch 22) before caching, so 429s are never cached
7  UseResponseCompression     (Ch 11) wraps everything below — each response compressed per request, incl. cache hits
8  UseResponseCaching         (Ch 9) HTTP/browser semantics
9  UseOutputCache             (Ch 10) server cache — endpoint-aware, after UseRouting
10 UseAuthentication          crypto work — as late as correct auth allows
11 UseAuthorization
```

Rules that generate this order (any app, any middleware):

1. **Cheap, high-frequency short-circuits as early as correct**: static files, 404s, health checks (Ch 29) — everything below them never runs.
2. **Endpoint-aware stages need `UseRouting` first** (rate limiter partitions by user; output cache varies by endpoint).
3. **Guard stages before content stages**: rate-limit *before* cache (never cache rejections); auth *after* things anonymous users may receive (static files), *before* anything that leaks data.
4. **Compression wraps the caches**: registered before them, it compresses every response on the way out — including output-cache hits — so `Accept-Encoding` negotiation is always per request. This matters for correctness: the caches store *uncompressed* bodies, and a stored compressed body could be served to a gzip-only client as brotli (a bug). The re-compress cost is ~0.2 ms on our payloads (measured in Ch 11); if you ship megabyte payloads, move compression inboard of the caches and measure the trade.

##### 8.2 Custom middleware, two ways, zero allocations

**Inline (the `Use` lambda)** — add this to *both* `Program.cs` files, right after `builder.Build()`'s error-handling lines (so in production it sits inside the exception handler):

```csharp
app.Use(async (ctx, next) =>                        // 1
{
    var start = Stopwatch.GetTimestamp();           // 2
    ctx.Response.OnStarting(() =>                  // 3
    {
        var elapsed = Stopwatch.GetElapsedTime(start);          // 4
        ctx.Response.Headers["X-Elapsed-Ms"] =
            elapsed.TotalMilliseconds.ToString("F1");
        return Task.CompletedTask;                 // 5
    });
    await next();                                  // 6
});
```

Line by line — this is our second real async pattern, so read it slowly:

- **1 — `app.Use(async (ctx, next) => {...})`**: register one stage. `ctx` is the `HttpContext` (request+response+state); `next` is a `RequestDelegate` — "invoke the rest of the pipeline". The lambda runs *once per request*; the compiler caches it, so the per-request cost is what happens inside, not the registration.
- **2 — `Stopwatch.GetTimestamp()`**: reads the high-resolution performance counter — a *static* call, **no allocation**. The naive alternative, `Stopwatch.StartNew()`, allocates a class instance per request: harmless once, wasteful at 33 rps × every request. Chapter 24 generalizes this discipline.
- **3 — `ctx.Response.OnStarting(...)`**: registers a callback that fires *just before the response headers are written*. This is the correct hook for headers that depend on work not yet finished (like total elapsed). Setting headers after `await next()` returns is usually too late — the response may already be flushing, and header writes would throw or vanish.
- **4 — `Stopwatch.GetElapsedTime(start)`** (.NET 7+): converts the raw counter into a `TimeSpan` using the OS frequency — again, allocation-free.
- **5 — `return Task.CompletedTask`**: the callback contract is `Func<Task>`; returning the already-completed singleton avoids allocating a `Task` per response (a preview of `ValueTask`, Chapter 13).
- **6 — `await next()`**: run everything deeper; when it returns, the response is (almost) done. Our elapsed time spans the whole inner pipeline — exactly what we want to measure.

**Class-based (when middleware needs dependencies)**:

```csharp
public class ElapsedMsMiddleware
{
    private readonly RequestDelegate _next;                   // 1

    public ElapsedMsMiddleware(RequestDelegate next) => _next = next;

    public async Task InvokeAsync(HttpContext ctx)            // 2
    {
        // ...identical body to the inline version...
        await _next(ctx);                                    // 3
    }
}
// registration:  app.UseMiddleware<ElapsedMsMiddleware>();
```

- **1** — the class is constructed **once** (a singleton in the pipeline). Any expensive-to-build state (parsers, caches of compiled regex) belongs here, in fields.
- **2 — `InvokeAsync(HttpContext)`**: convention-resolved by name; its extra parameters are injected from DI per request — that is how middleware can take scoped services without becoming scoped itself.
- **3 — `await _next(ctx)`**: same "call the rest" contract as the lambda's `next()`.

Which to use? Inline for trivial stages; a class the moment you want DI or unit tests. Both are allocation-free per request as written.

##### 8.3 Per-request state done right

Need to pass data between stages? `ctx.Items["key"] = value` — a per-request dictionary that dies with the request. Do **not** build per-request state via closures over mutable variables or (worst) `static` fields — a static field is shared by all concurrent requests: a data race and, under load, a wrong-data bug, not just a performance one. The one anti-pattern to memorize: **never allocate per-request buffers in middleware** — Chapter 24 replaces per-request `byte[]` with `ArrayPool<T>`; we simply note it here because middleware is where "once per request" hides.

##### 8.4 Applying it to ShopX + measuring

Changes made: the `X-Elapsed-Ms` middleware added to both apps; (order for the template apps was already correct — `UseStaticFiles` precedes `UseRouting`).

Restart both apps, run the Chapter 5 load, and read the header with curl:

```text
curl -s -D - -o NUL http://localhost:5080/api/products?page=1 | findstr X-Elapsed-Ms
X-Elapsed-Ms: 213.4          (illustrative — will move as later chapters land)
curl -s -D - -o NUL http://localhost:5180/css/site.css | findstr X-Elapsed-Ms
X-Elapsed-Ms: 0.3            (static short-circuit: nothing below UseStaticFiles ran)
```

That 0.3 ms *is* the short-circuit lesson, measured: the same request shape that costs 213 ms through the full pipeline costs nothing once it is answered early.

LAB.md **E-06 — Pipeline instrumented**: no material latency change expected (this chapter added instrumentation, not optimization) — the honest entry is "no change by design; X-Elapsed-Ms live". The first real lever lands in Chapters 9–10.

#### Key takeaways

- Order rules: early short-circuits; endpoint-aware after `UseRouting`; guards (rate limit) before caches; auth as late as correctness allows; compression wraps producers.
- `app.Use(async (ctx, next) => …)` = one stage; short-circuit = don't call `next()`.
- Write middleware allocation-free: `Stopwatch.GetTimestamp`, `Task.CompletedTask`, fields in a class-based singleton; `OnStarting` for headers that depend on the outcome.
- `X-Elapsed-Ms` now measures ShopX's inner pipeline on every response.

#### Exercises

**Exercise 8.1 — Order the stages.** Place these in a correct order and justify: `UseAuthorization`, `UseStaticFiles`, `UseRateLimiter`, `UseRouting`, `UseOutputCache`, `UseExceptionHandler`.

*Solution.* `UseExceptionHandler` → `UseStaticFiles` → `UseRouting` → `UseRateLimiter` → `UseOutputCache` → `UseAuthorization`. Reasoning: handler outermost so it can catch and render errors; static files short-circuit before routing work; limiter after routing (endpoint/user partitioning) but before the cache (429s must never be cached) and before output cache; authorization last among them so anonymous-safe content isn't gated by crypto. Any order that caches a 429 or compresses nothing fails the test.

**Exercise 8.2 — Find the bug.** A teammate's middleware sets a response header after `await next()` without `OnStarting`. What goes wrong, and when exactly?

*Solution.* By the time `next()` returns, the response may have started flushing (headers sent) — `Headers[...] = ...` then either throws (`InvalidOperationException`) or is silently dropped for already-sent headers. It appears to work in tiny local samples (response not yet flushed) and breaks intermittently under real payloads — the worst kind of bug. Fix: register the write in `OnStarting`, or set the header before calling `next()` when the value is known upfront.

#### Interview questions

**Q: What is `RequestDelegate`?**
A: `Func<HttpContext, Task>` — "the rest of the pipeline as one callable". Each middleware receives it in its constructor (class form) or closure (inline form) and invokes it to pass control; not invoking it short-circuits the request. The chain is built once at startup by composing these delegates.

**Q: Why is class-based middleware a singleton, and how can it use scoped services?**
A: The pipeline instance is built once; making it scoped would require rebuilding the chain per request. Instead, the runtime injects scoped services into `InvokeAsync`'s *parameters* per request — the middleware stays singleton, its per-request dependencies do not.

**Q: Give one ordering bug you have seen (or can imagine) and its user-visible symptom.**
A: Response/output caching placed before authentication: an authenticated page gets cached, then served (with the first user's data) to other users — a security leak, not just a perf issue. Symptom: "users see someone else's profile". Fix: caches after auth, plus `Vary`/no-cache rules for personalized content (Chapters 9–10 formalize the cache-safety rules).

#### Performance checklist

- [ ] I can reproduce the order chart from the four rules (not from memory of the list)
- [ ] `X-Elapsed-Ms` shows on both apps; I verified the static-file short-circuit reads ~0.x ms
- [ ] My custom middleware (if any) allocates nothing per request — checked with the Ch 6 profiler
- [ ] Per-request state uses `HttpContext.Items`, never static fields
- [ ] E-06 recorded with "no change by design"

---
### Chapter 9: Response Caching and the Browser

#### What this chapter is about

The cheapest server is the one that does not receive the request. This chapter teaches HTTP caching semantics — the protocol every browser, proxy, and CDN on earth speaks — and ASP.NET Core's two-layer implementation: `[ResponseCache]` (writes the headers) plus `UseResponseCaching` (a small built-in server-side store). You will apply it to ShopX, watch 304s happen, and learn the measurement trap that catches every junior load-tester: **caches change your benchmark**.

#### A short history of this topic

HTTP/1.0 added `Expires` (1996); HTTP/1.1 (1997) formalized `Cache-Control`, `ETag`, and the validation model — the 304 Not Modified dance that still runs the web. CDN economics (Akamai from 1998) turned header hygiene into a business: a header worth 60 seconds of freshness is a 60-second discount on your origin server. ASP.NET Core's `UseResponseCaching` shipped in 1.0 (2016) as a convenience store; Chapter 10's OutputCache (2022) is its modern successor for server-side caching — you will use both, and know exactly when each applies.

#### What it provides you

- Fluency in `Cache-Control`, `Vary`, `ETag`, `If-None-Match`, and the 304 flow — the vocabulary of every cache you will ever configure, including CDNs.
- A first real optimization on ShopX: repeat views of the catalog stop touching your code entirely.

#### Why you should use it

Because browser/CDN hits are *free to you*: no CPU, no database, no bandwidth bill for the body (304 responses carry ~200 bytes). For public, non-personalized content, this is the highest-leverage, lowest-risk cache in the entire stack.

##### 9.1 The protocol in one section

**Freshness (the server decides):** `Cache-Control` on a response tells every cache along the path how long the response may be reused without asking:

| Header | Meaning | Use on ShopX |
|---|---|---|
| `Cache-Control: public, max-age=60` | any cache may store; fresh for 60 s | catalog pages, product lists |
| `private` | only the user's own browser may store | (rare; we have no per-user cacheable pages) |
| `no-cache` | store, but revalidate every time | dashboards |
| `no-store` | never store anything | carts, checkout, auth pages |
| `Vary: <header>` | cache separately per value of that header | `Vary: Accept-Encoding` (Ch 11) |

**Validation (the cache asks):** once stale, a cache sends its saved validator back: `If-None-Match: "abc123"` (an `ETag` — an opaque version tag the server invented) or `If-Modified-Since` (a timestamp). The server either sends the full body (200) or just `304 Not Modified` — body omitted, client reuses its copy:

```
GET /catalog?page=1                      (stale copy in browser)
  If-None-Match: "8f3a…"
─────────────────────────────────────────────►
◄────────────────────────────────────────────
HTTP/1.1 304 Not Modified                (no body; browser renders its cached copy)
```

##### 9.2 ASP.NET Core's implementation, honestly stated

`[ResponseCache(Duration = 60, Location = ResponseCacheLocation.Any, VaryByQueryKeys = new[]{"page"})]` writes those headers. `app.UseResponseCaching()` *additionally* keeps a small in-process store of responses and may answer requests from it (GET/HEAD, 200s only; it refuses to cache responses with `Set-Cookie`, and it sits after `UseCors` per the order chart). It is deliberately minimal: no eviction API, no tags, no distribution. It is the right tool when you want protocol headers *and* a bonus local store; Chapter 10's OutputCache is the right tool when you want policy, eviction, and tags. Most apps end up using OutputCache for server-side work; knowing both keeps you bilingual in code reviews.

##### 9.3 Applying it to ShopX

`CatalogController.Index` (MVC) — one attribute, one line of registration. In `ShopX.Web/Program.cs`, add after `UseRouting` (per the Chapter 8 chart):

```csharp
app.UseResponseCaching();
```

Then on the action:

```csharp
[ResponseCache(Duration = 60, Location = ResponseCacheLocation.Any,
               VaryByQueryKeys = new[] { "page" })]
public async Task<IActionResult> Index(int page = 1, int pageSize = 20)
```

Word by word: `Duration = 60` → `max-age=60`; `Location.Any` → `public` (browser *and* shared proxies); `VaryByQueryKeys = new[]{"page"}` → separate cache entries per page number (`Vary: Query` header) — without it, page 1's HTML could be served for page 7: a correctness bug that caching can introduce, which is why cache-safety is a checklist item forever. `VaryByQueryKeys` **requires** `UseResponseCaching` to be registered — you get a runtime exception otherwise, not a silent miss.

Add the same attribute to `ProductsController.GetList` in the API (`VaryByQueryKeys = new[]{"page","pageSize"}`).

##### 9.4 Watching it work

```text
> curl -s -D - -o NUL "http://localhost:5180/catalog?page=1"
HTTP/1.1 200 OK
Cache-Control: public,max-age=60
Vary: Query
X-Elapsed-Ms: 228.9            ← first hit: full pipeline (illustrative)

> curl -s -D - -o NUL "http://localhost:5180/catalog?page=1"
HTTP/1.1 200 OK
X-Elapsed-Ms: 1.1              ← served by UseResponseCaching's local store
```

In Edge/Chrome DevTools (Network tab, "Disable cache" unchecked), navigate to the catalog, then reload: subsequent loads show *"(memory cache)"* or a 304 — the user's repeat navigation now costs your server nothing but the connection accept.

##### 9.5 The measurement trap (this section will save you an embarrassing week)

Re-run Chapter 5's k6 script unchanged. Surprise (illustrative): `web-catalog` p95 collapses from 540 ms to ~25 ms. Did your app get 20× faster? **No — your load test started measuring the cache, not the app.** Our script cycles pages 1–10; after the first pass, every request is a local-store hit. Both numbers are *true*; they answer different questions:

- **Hit path** (what users experience on repeat views, warm caches): p95 ~25 ms.
- **Miss path** (cold cache, first visitor, varied pages — the number that reflects your code): unchanged, ~540 ms.

To measure the miss path under load, bust the cache by varying the key dimension:

```js
http.get(`${BASE_WEB}/catalog?page=${page}&_r=${__ITER}`, { tags: { name: 'web-catalog' } });
```

`_r=${__ITER}` makes every iteration's URL unique, so no cache can ever answer it. (The `_r` parameter is ignored by the action; binding does not care about extra query keys — but `VaryByQueryKeys` means the *store* does.) Record both:

- **E-07a — catalog, cache hits:** p50/p95 = 21/25 ms (illustrative).
- **E-07b — catalog, forced misses:** p50/p95 ≈ baseline (231/540 ms) — the honest "code as written" number, now with a plan: Part IV attacks it.

Golden rule going forward, now a permanent line in your method: **every load test must state what the cache was doing.** In Chapter 28 this becomes formal scenario design (cache-hit and cache-miss scenarios, separately reported).

#### Measuring the change

E-07a/E-07b as above, plus one caveat line in LAB.md: response caching helps *repeat* views of public content; it does nothing for first visitors, per-user pages (carts), or POSTs. Those need Chapter 10 (server-side, per-endpoint policy) and Part V.

#### Key takeaways

- `Cache-Control`/`Vary` control freshness; `ETag`/`If-Modified-Since` + 304 control revalidation — learn them once, use them in every cache and CDN for the rest of your career.
- `[ResponseCache]` writes headers; `UseResponseCaching` adds a minimal in-process store (GET/200 only, never with `Set-Cookie`); OutputCache (next chapter) supersedes it server-side.
- `VaryByQueryKeys` is a correctness tool (page 1 must never be served for page 7) and requires the middleware.
- Caches change benchmarks: report hit-path and miss-path separately, and bust caches explicitly (`&_r=${__ITER}`) when you want the miss path.

#### Exercises

**Exercise 9.1 — Compute the freshness.** Response A: `Cache-Control: public, max-age=30`. Response B: `Cache-Control: no-cache, must-revalidate` with an ETag. Response C: no Cache-Control at all, `Last-Modified: Mon, 02 Nov 2026 10:00:00 GMT`. Describe each cache's behavior over the next minute.

*Solution.* A: reusable without contact for 30 s; after that, no validator is present, so caches *may* heuristically revalidate (or just refetch); you cannot rely on 304s. B: may be stored but every use revalidates with `If-None-Match` — 304 if unchanged, else 200. C: no explicit freshness — HTTP allows heuristic freshness (typically 10% of age-since-Last-Modified); revalidation uses `If-Modified-Since`. Lesson: say what you mean; leaving headers off hands the decision to every cache on the path.

**Exercise 9.2 — Why no `Set-Cookie`?** Explain the design rule "`UseResponseCaching` never caches responses carrying `Set-Cookie`" as a security argument.

*Solution.* A `Set-Cookie` marks the response as *personal* (a session being established). Caching such a response in a *shared* store risks serving one user's session-bearing page to another user — session hijacking by cache. The middleware cannot know which cookie is sensitive, so it conservatively refuses; the same rule is why Chapter 10's cache-safety rules forbid caching authorized content. When you see a cache "not working", check for a cookie that snuck onto the response.

#### Interview questions

**Q: ETag vs Last-Modified — when do you care?**
A: Both are validators for 304 revalidation. Last-Modified has 1-second resolution and no semantics for "changed then reverted"; ETags are opaque and precise (content hash or version) and win for anything generated or versioned. Cost note: computing a strong ETag per request can itself be work — for large payloads, a cheap weak ETag (W/) from a row version beats re-serializing the body.

**Q: A user reports seeing another user's data. Where do you look first?**
A: Cache placement. Caches (response cache, output cache, CDN) placed before authentication, or caching authorized/personalized responses, replay one user's content to others. Verify order (Chapter 8), verify `no-store` on personalized pages, and check for `Vary` gaps. This class of bug is why cache-safety rules exist in this book as checklists.

**Q: Why does the book measure cache-hit and cache-miss paths separately?**
A: Because they answer different questions: hit path = experienced latency for repeat traffic; miss path = the cost of the code as written. Reporting only one is how teams "optimize" by accident (their load test just started hitting a cache) or miss real regressions (all traffic is misses in production after a deploy empties caches). Chapter 28 makes both first-class scenarios.

#### Performance checklist

- [ ] I can write the `Cache-Control` table from memory and pick the right directive per content type
- [ ] `[ResponseCache]` + `UseResponseCaching` applied to catalog + products list; 304s observed in DevTools
- [ ] `VaryByQueryKeys` understood as a correctness requirement (and its middleware dependency)
- [ ] Load tests now report hit-path vs miss-path; cache-busting param in my k6 toolkit
- [ ] E-07a/E-07b in LAB.md with my numbers

---
### Chapter 10: Output Caching on the Server

#### What this chapter is about

Response caching (Chapter 9) helps *repeat views* through shared caches. Output caching helps the *server itself*: it stores the fully-rendered response — status, headers, body — and serves subsequent matching requests **without running your action, your EF queries, or your serializer**. On ShopX's hit path this deletes the top two rows of the Chapter 7 budget table (~60% EF + ~19% JSON) in one stroke. You will add policies, tags, write-time invalidation, and learn the cache-safety rules that keep this powerful feature from becoming a data-leak.

#### A short history of this topic

Full-page caching is the oldest web trick: classic ASP.NET had `<%@ OutputCache %>` page directives (2002). ASP.NET Core 1.0–6 shipped *none* — the rewrite's stateless pipeline made the old mechanism impossible, and teams hand-rolled `IMemoryCache` blobs (Chapter 18 does this once, on purpose). .NET 7 (November 2022) brought it back properly: `[OutputCache]` + `AddOutputCache`, with policies, Vary dimensions, *tags*, built-in per-key locking, and a swappable store — effectively "page output caching, redesigned for middleware".

#### What it provides you

- Per-endpoint cache policies (expiry, vary keys, tags) declared in one place.
- Write-time invalidation: placing an order evicts every catalog-related entry in one call.
- Built-in single-flight locking: concurrent identical misses wait for the first one — your first taste of stampede protection (formalized in Chapter 19).

#### Why you should use it

The hit-path arithmetic. A cached hit costs ~0.4 ms (store lookup + body write) versus ~214 ms mean for the same request computed (illustrative). That is not a percentage improvement; it is a different category of cost — the request becomes cheaper than its own TCP handling. No amount of micro-optimizing the EF query inside (Part IV) can compete with not running it.

##### 10.1 How it works

`AddOutputCache` registers a store (in-memory by default; swappable — Chapter 20 notes the distributed option). `app.UseOutputCache()` places the stage after `UseRouting`. When a request arrives, the middleware builds a **cache key** — endpoint + the varied dimensions — looks it up, and on a hit: writes the stored response and **returns without calling `next()`** (Chapter 7's short-circuit, at full strength). On a miss, it runs the pipeline, intercepts the produced response, stores a copy (keyed, tagged), and sends it on. Concurrent misses for the same key are *held* until the first one completes (locking) — one fill, then everyone hits.

##### 10.2 Policies, applied to ShopX

**`ShopX.Api/Program.cs`** — register the store + policies:

```csharp
builder.Services.AddOutputCache(options =>
{
    options.AddBasePolicy(b => b.Expire(TimeSpan.FromSeconds(30)));        // 1

    options.AddPolicy("ProductsList", b => b
        .Expire(TimeSpan.FromMinutes(2))                                    // 2
        .VaryByQuery("page", "pageSize")                                    // 3
        .Tag("catalog"));                                                   // 4

    options.AddPolicy("ProductDetail", b => b
        .Expire(TimeSpan.FromMinutes(5))
        .Tag("catalog"));
});
```

and after `UseRouting` (order chart, Chapter 8):

```csharp
app.UseOutputCache();
```

Line by line:

- **1 — `AddBasePolicy`**: applies to every `[OutputCache]` endpoint that names no policy — here, a conservative 30 s floor.
- **2 — `.Expire(...)`**: absolute time-to-live for the stored entry.
- **3 — `.VaryByQuery("page", "pageSize")`**: those query keys participate in the cache key. Miss one and you serve page 1's body for page 7 — the same correctness rule as `VaryByQueryKeys` in Chapter 9. (Route values — the `{id}` of `/api/products/42` — are part of the key automatically: different path, different entry.)
- **4 — `.Tag("catalog")`**: names this family of entries so a write can evict them all at once.

Then annotate the actions (Api):

```csharp
[OutputCache(PolicyName = "ProductsList")]
public async Task<IActionResult> GetList(int page = 1, int pageSize = 20) { ... }

[OutputCache(PolicyName = "ProductDetail")]
public async Task<IActionResult> GetDetail(int id) { ... }
```

**`ShopX.Web/Program.cs`**: the same `AddOutputCache` (policies `CatalogPage`: `.Expire(TimeSpan.FromMinutes(2)).VaryByQuery("page").Tag("catalog")` and `CatalogDetail`: 5 min, tagged) plus `app.UseOutputCache()`; then:

```csharp
[OutputCache(PolicyName = "CatalogPage")]
public async Task<IActionResult> Index(int page = 1, int pageSize = 20) { ... }
```

Remove Chapter 9's `[ResponseCache]` attributes from these actions — we now serve them from OutputCache; keeping both would layer two stores confusingly. (Keep the Chapter 9 knowledge: headers still matter for CDNs — OutputCache also honors `NoStore`, and you will still use `Cache-Control` on genuinely static endpoints in real systems.)

##### 10.3 Eviction on write — the CacheInvalidator

Caching without an invalidation story is a staleness bug with a timer. Add `Services/CacheInvalidator.cs` to `ShopX.Api`:

```csharp
using Microsoft.AspNetCore.OutputCaching;

namespace ShopX.Api.Services;

public class CacheInvalidator                        // 1
{
    private readonly IOutputCacheStore _store;       // 2
    public CacheInvalidator(IOutputCacheStore store) => _store = store;

    public async Task EvictCatalogAsync(CancellationToken ct = default) =>
        await _store.RemoveByTagAsync("catalog", ct);    // 3
}
```

- **1** — a plain scoped service (constructor-injected like everything else; register with `builder.Services.AddScoped<CacheInvalidator>();`).
- **2 — `IOutputCacheStore`**: the store abstraction `AddOutputCache` registered (in-memory default). Injecting it lets *any* code evict — the write path does not know about controllers.
- **3 — `RemoveByTagAsync("catalog", ct)`**: deletes every entry carrying that tag — both apps' lists and details, all pages, one call.

Call it from the write path — `OrdersController.Place`, after `SaveChangesAsync()`:

```csharp
await _invalidator.EvictCatalogAsync();   // stock changed → lists/details are stale
```

(inject `CacheInvalidator _invalidator` in the constructor alongside `ShopXContext _db`). The sequence matters: evict *after* the transaction commits, otherwise a concurrent request can re-fill the cache with the pre-order (now wrong) data — a classic race. That one line is the entire write-side story: **write commits → tags evicted → next read re-fills**.

##### 10.4 The cache-safety rules (memorize, then enforce in review)

1. **Never cache authorized or personalized content.** ShopX's cart endpoints carry no `[OutputCache]` — ever. (This rule is why the order chart puts caches after auth.)
2. **Never cache a response carrying `Set-Cookie`** — personal by definition.
3. **Vary on every input that changes the output.** Our lists vary by `page`/`pageSize`; forget one and you serve wrong content *correctly formatted* — the worst kind of bug.
4. **Invalidate at write time via tags; TTL is a backstop, not the plan.** Two minutes of stale stock on the catalog is our explicit product decision — recorded in LAB.md so it is a decision, not an accident.
5. **Health endpoints are never cached** (Chapter 29) — a cached liveness signal is how you autoscale into an outage.

##### 10.5 Measuring it

Run the k6 load (pages 1–10 cycling, as before) — illustrative results:

| Metric | Ch 9 state | Ch 10 (hits) | Ch 10 (forced misses) |
|---|---|---|---|
| `/api/products` p95 | 471 ms (uncached) | **~38 ms** | ~471 ms (unchanged — by design) |
| `X-Elapsed-Ms` on a hit | — | 0.3–0.6 ms | ~214 ms mean |
| EF queries per hit | 22 | **0** | 22 |
| `/catalog` p95 | 540 ms | **~44 ms** | ~540 ms |

Two readings for LAB.md: **E-08a — hit path** (p95 38 ms; zero SQL statements in the Database profiler timeline during steady state; allocations per hit collapse to ~9 KB — framework + body write only) and **E-08b — miss path** (unchanged, and that is *correct*: this chapter bought the hit path; Part IV buys the miss path).

Memory note for Chapter 23: the store now holds ~10 page variants × 16.8 KB (API) + 10 × ~86 KB (HTML, Ch 5's pager weight) ≈ **~1 MB** — fine on a dev box, and the number to watch: cache size grows with key cardinality (page × category × sort multiplies it in Ch 17), so cache *size* is a budget line, not vibes.

#### Measuring the change

E-08a/E-08b recorded. The scoreboard's headline moves: hit-path p95 for the API 471 → 38 ms (illustrative) — the first big win of the book, from ~30 lines of configuration. The miss path is untouched, and honesty about that is what your team will trust.

#### Key takeaways

- OutputCache (servers .NET 7+) caches whole responses: hit = no action, no EF, no JSON — `X-Elapsed-Ms` drops below 1 ms.
- Policies (`Expire`, `VaryByQuery`, `Tag`) are declared once; `[OutputCache(PolicyName=…)]` selects them; route values are in the key automatically.
- Built-in locking dedupes concurrent misses (single-flight); first taste of Chapter 19's stampede theory.
- Evict at write time (`RemoveByTagAsync`), after the transaction commits; TTL is the backstop.
- The five cache-safety rules — break one and caching becomes a leak or a wrong-content bug.

#### Exercises

**Exercise 10.1 — Design the vary set.** The product list gains `?category=` and `?sort=price|name` in Chapter 17. What must the policy look like, and what goes wrong with each key you forget?

*Solution.* `.VaryByQuery("page", "pageSize", "category", "sort")`. Forget `category` → searches for "Climbing" return Trekking results; forget `sort` → price-sorted pages served name-sorted; forget `page` → every page shows page 1. Each failure is *silent* (valid 200s with wrong content) — which is why the vary set is reviewed like a schema, and why Chapter 28's load test deliberately walks distinct combinations.

**Exercise 10.2 — The eviction race.** A junior places `EvictCatalogAsync()` *before* `await SaveChangesAsync()`. Construct the failure sequence.

*Solution.* T0: request A commits not yet — it evicts tags; T1: request B (a read) misses, starts re-filling from **pre-write** data (the transaction hasn't committed, so B still sees old stock); T2: B's response is stored under the tag — with stale data; T3: A commits. Result: cache holds pre-order content until TTL, despite the "invalidation having run". Fix: evict after commit (as in 10.3). Generalize: invalidation must be ordered *after* the state it invalidates becomes visible.

#### Interview questions

**Q: ResponseCache vs OutputCache — when each?**
A: `[ResponseCache]` speaks HTTP (`Cache-Control`/ETag) for downstream caches — browsers, CDNs; add `UseResponseCaching` for a small local store. OutputCache is the server-side policy engine: expiry, Vary by query/route/value, tags, programmatic eviction, locking, pluggable store. Use both when you serve through a CDN (headers for the edge, OutputCache for the origin); choose OutputCache alone for pure API origins.

**Q: How does OutputCache behave under a burst of misses for the same key?**
A: Built-in locking: the first request computes and fills while concurrent identical requests wait, then all are served from the store — one fill per key (single-flight). That prevents a stampede of identical misses from hammering the database — the same idea you will hand-roll for `IMemoryCache` in Chapter 18 and get for free from `HybridCache` in Chapter 20.

**Q: Where do cache-safety rules come from — why "never cache authorized content"?**
A: Because a shared, keyed store has no notion of *who* asks. Any personalized or authorization-gated response cached under a key that ignores identity will be replayed to the next requester of that key — cross-user data leaks. Hence: caches after auth in the pipeline; `no-store` on personalized responses; vary on identity only when you truly mean per-user caching (rare, and expensive — it multiplies entries by user count).

#### Performance checklist

- [ ] Both apps use `AddOutputCache` policies; `[OutputCache]` on list/detail actions only (never cart/order write or auth paths)
- [ ] `CacheInvalidator` evicts `catalog` tag after `SaveChanges` in `Place`
- [ ] Hit-path X-Elapsed-Ms < 1 ms observed with curl; Database profiler shows zero SQL during steady load
- [ ] Vary sets reviewed per endpoint like a schema (page/pageSize today; category/sort added in Ch 17)
- [ ] E-08a (hits) and E-08b (misses) in LAB.md, separately labeled

---
### Chapter 11: Faster Payloads with Compression and System.Text.Json

#### What this chapter is about

Two costs live in every response: *CPU to build the bytes* (serialization) and *network to ship them* (payload size). This chapter attacks both: response compression (Brotli/Gzip middleware) and source-generated JSON (`System.Text.Json` without reflection). It also sets an expectation you will meet constantly in performance work: some optimizations barely move your localhost benchmark and dramatically improve real users' lives — knowing which is which is the difference between an engineer and a benchmark tourist.

#### A short history of this topic

Gzip (1992) made compression affordable on the web; Brotli (Google, 2015, shipped in browsers 2016–2018) gives ~15–25% better ratios on text at similar speed. `System.Text.Json` (2018, .NET Core 3.0) replaced Newtonsoft.Json as the default in ASP.NET Core 3+; .NET 6 added **source generation** — the serializer's metadata (what reflects on which property) computed at *compile time* by a generator instead of at runtime by reflection. In one stroke that removed the reflection cost, the startup metadata cost, and unlocked Native AOT (Chapter 25). Serialization speed now ships as an attribute.

#### What it provides you

- `UseResponseCompression`: ~74% smaller payloads for text endpoints (illustrative), negotiated per request via `Accept-Encoding`.
- `JsonSerializerContext` source generation: reflection-free serialization for every *named* type you register.
- The discipline of measuring transfer costs separately from compute costs.

#### Why you should use it

Serialization was ~19% of ShopX's CPU budget (Chapter 6/7); the wire carried 16.8 KB where ~4.3 KB would do. Both are pure waste — no user ever asked for them. And compression is middleware: the whole change is configuration, reviewable in one diff.

##### 11.1 Compression: the theory you need

- **Negotiation:** clients advertise support in `Accept-Encoding: br, gzip`; the middleware picks the first configured provider the client supports, adds `Content-Encoding: br` and `Vary: Accept-Encoding` (so caches — Chapter 9's rule — never serve Brotli bytes to a Gzip-only client).
- **Providers:** Brotli first (better ratio at similar CPU), Gzip as fallback. Old clients that send neither simply receive identity — the middleware never forces.
- **Levels:** `CompressionLevel.Fastest` ≈ gzip-class ratio, a fraction of the CPU of Optimal; `Optimal` spends milliseconds to save a few more percent. Rule for *dynamic* content: Fastest — the CPU is on your request path. Pre-compress *static* assets at build time at maximum level instead (Chapter 27 does exactly that for `site.css`).
- **When not to compress:** payloads under ~1 KB (framing overhead eats the win), anything already compressed (JPEG/PNG/ZIP — you burn CPU for ~1%), and servers already at 100% CPU (compression is a CPU-for-bytes trade; Chapter 1 taught you to name it).

`EnableForHttps = true` — response compression is off for HTTPS by default (the BREACH-attack concern: compressed responses can leak secrets when attackers can control reflected input). We enable it in the lab; in production, enable it only after confirming your responses don't reflect attacker-controlled text next to secrets — then record that decision in LAB.md.

##### 11.2 Wiring compression into ShopX

Both `Program.cs` files (registration position per the Chapter 8 chart: *before* `UseResponseCaching`/`UseOutputCache`, so every response — including cache hits — is compressed per request):

```csharp
builder.Services.AddResponseCompression(o =>
{
    o.EnableForHttps = true;                                  // 1
    o.Providers.Add<BrotliCompressionProvider>();              // 2 — first listed = preferred
    o.Providers.Add<GzipCompressionProvider>();
});
builder.Services.Configure<BrotliCompressionProviderOptions>(
    o => o.Level = CompressionLevel.Fastest);                  // 3
builder.Services.Configure<GzipCompressionProviderOptions>(
    o => o.Level = CompressionLevel.Optimal);
```

```csharp
app.UseResponseCompression();   // before UseResponseCaching / UseOutputCache
```

- **1** — the HTTPS switch discussed above.
- **2** — provider order is the preference order; `Accept-Encoding` negotiation does the rest.
- **3** — Fastest for Brotli (dynamic content rule), Optimal for the legacy Gzip fallback.

The default MIME list already covers JSON, HTML, CSS, SVG and friends — extend `o.MimeTypes` only if you serve something exotic (the property exists for that).

Measure with curl (note `Content-Encoding` and the byte count — `-o NUL` discards the body but still receives it):

```text
> curl -s -D - -o NUL --compressed "http://localhost:5080/api/products?page=1"
Content-Encoding: br
Vary: Accept-Encoding
```

Illustrative scoreboard line for the wire (sizes from my machine): list JSON **16.8 KB → 4.3 KB (−74%)**; catalog HTML **~86 KB → ~20 KB (−77%)**. Now the honest part: re-run k6. `http_req_duration` barely moves — on localhost, transferring tens of KB fewer costs well under a millisecond. **Compression is not a server-latency optimization; it is a user-network optimization.** Your 4G user downloading 4.3 KB instead of 16.8 KB at ~1 MB/s saves ~13 ms of *their* time per request and, multiplied by every response your API ships, real bandwidth money. Record it as such in LAB.md (E-09: wire −74%/−77%; server p95 ~unchanged; CPU +0.2 ms/req — the trade, named).

##### 11.3 System.Text.Json: from reflection to source generation

What MVC's JSON formatter does by default: at runtime, reflect over each type (`Product`, its `Category`, the anonymous wrapper), build metadata caches, then write. Reflection works, is cached after first use, and still costs — CPU on every call and allocations the GC later pays for (Chapter 23). Source generation moves the metadata to compile time: a generator emits a `JsonSerializerContext` class with pre-built converters — no reflection at runtime, smaller memory, and it is the *only* path that works under Native AOT.

Add `AppJsonContext.cs` to `ShopX.Api`:

```csharp
using System.Text.Json.Serialization;
using ShopX.Data.Entities;

namespace ShopX.Api;

[JsonSourceGenerationOptions(PropertyNamingPolicy = JsonKnownNamingPolicy.CamelCase)]   // 1
[JsonSerializable(typeof(Product))]     // 2 — every NAMED type we serialize
[JsonSerializable(typeof(Category))]
[JsonSerializable(typeof(Order))]
[JsonSerializable(typeof(OrderItem))]
public partial class AppJsonContext : JsonSerializerContext { }                          // 3
```

Wire it into MVC's formatter chain:

```csharp
builder.Services.AddControllers()
    .AddJsonOptions(o => o.JsonSerializerOptions.TypeInfoResolverChain.Insert(0, AppJsonContext.Default));   // 4
```

- **1 — `[JsonSourceGenerationOptions]`**: compile-time mirror of the runtime options. `CamelCase` matches MVC's default web behavior (`{"unitPrice":...}`) — **the options must mirror what reflection produced, or you change the response shape** — behavior change, forbidden by Chapter 1's test 1.
- **2 — `[JsonSerializable(typeof(Product))]`**: one line per named type the generator must cover. `Product` covers `GetDetail` (it returns a `Product`).
- **3 — `partial class … JsonSerializerContext`**: the generator writes the other half of this class (`AppJsonContext.Default` is the generated instance).
- **4 — `TypeInfoResolverChain.Insert(0, …)`**: ask the source-gen resolver first; anything it does not cover (our anonymous wrappers — see below) falls through to the default reflection resolver. Order = priority.

**The honest limitation (and the setup for Chapter 16):** `GetList` returns `Ok(new { total, page, pageSize, items })` — an *anonymous* type. You cannot write `[JsonSerializable(typeof(new{...}))]`; anonymous types have no name to reference. So today, the **detail endpoint** (`Product`, named) serializes via source-gen, and the **list endpoint** keeps reflection. Chapter 16 replaces the anonymous wrappers with named DTOs (which it needs anyway, for projection) — and at that point one more `[JsonSerializable]` line flips the list endpoint to source-gen too. This is not a cop-out; it is sequencing: Chapter 16's DTOs arrive for *database* reasons and finish this job for free. Optimization chapters often hand each other the baton like this.

Repeat the same wiring in `ShopX.Web` (it serializes nothing to JSON today — MVC renders HTML — so the context there is optional; add it in Chapter 29 when the health/JSON endpoints appear).

##### 11.4 Measuring it

Illustrative, from my machine (X-Elapsed-Ms + a BenchmarkDotNet-style micro-check of serializer cost on `Product`):

| Metric | Before Ch 11 | After Ch 11 |
|---|---|---|
| Wire: list JSON / catalog HTML | 16.8 / 86 KB | **4.3 / 20 KB** (−74% / −77%) |
| `GET /api/products/42` (detail, miss path) — serializer CPU | ~19% of request | **~12%** (source-gen; named type) |
| Detail endpoint allocations | ~96 KB | **~61 KB** |
| List endpoint (miss path) | reflection | reflection (unchanged — Ch 16 flips it) |
| Server p95 under k6 (both paths) | 471 / 38 ms | ~455 / 38 ms (wire savings invisible on localhost, CPU +0.2 ms) |

LAB.md **E-09** gets all of it, with the trade named in one line each: bytes −74% (users, bandwidth), CPU +0.2 ms/req (accepted), detail serialization −37% CPU/alloc (source-gen), list serialization unchanged (waiting on Ch 16 DTOs).

#### Key takeaways

- Compression: Brotli-first, `Fastest` for dynamic content, negotiated by `Accept-Encoding`, `Vary: Accept-Encoding` keeps caches safe; never compress <1 KB or already-compressed types; on HTTPS, decide `EnableForHttps` consciously (BREACH).
- Compression buys *users and bandwidth*, not localhost latency — measure and report it for what it is.
- Source-gen JSON: `[JsonSerializable(typeof(T))]` per named type, `TypeInfoResolverChain.Insert(0, context.Default)`; options must mirror the old output or you changed behavior.
- Anonymous wrapper types cannot be source-generated; named DTOs (Chapter 16) unlock them — chapters hand off batons.

#### Exercises

**Exercise 11.1 — The compression trade, quantified.** Your API ships a 40 KB JSON response 500 times/second. Brotli-Fastest reduces it to 10 KB at a CPU cost of 0.6 ms per response. Server CPU is currently 55%. Do you enable compression? Show the arithmetic.

*Solution.* Wire: 40 KB→10 KB saves 30 KB × 500 rps = 15 MB/s of egress — significant for users and bandwidth cost. CPU: +0.6 ms × 500 rps = 0.3 CPU-seconds per second = **+30 percentage points of one core** (across N cores, divide by N). On a 4-core box that is +7.5 points per core — acceptable at 55%+~8% ≈ 63%. On one core at 55%, you would hit 85% — reject or use `Optimal`-precomputed… actually the correct move there is: compress at Fastest (cheapest), or compress only responses >8 KB and cache the compressed form (OutputCache stores… remember Ch 10 stores uncompressed — a deeper option is caching *compressed* variants per encoding; that is a real advanced design, and the honest answer names the choice and its cost.

**Exercise 11.2 — Behavior-preservation check.** After enabling source-gen, a teammate reports `GET /api/products/42` now returns `{"id":42,"name":"…","unitPrice":…}` with **camelCase but different property order** and `createdAtUtc` missing. What rule was broken, and which knob fixes each symptom?

*Solution.* Rule: optimization must not change behavior. Property order: source-gen emits in declaration order of the *generated* metadata — align the context options with the old serializer's (or set `PropertyNamingPolicy`/order explicitly); in practice, JSON property order is not contractual for most clients — but "different" must be a *decision* (documented), not an accident. Missing `createdAtUtc`: the generator mirrors `JsonSerializerOptions` defaults — if reflection used default includes and the context was generated with `DefaultIgnoreCondition`… simplest cause: the entity property was `[JsonIgnore]`d or the context's options differ; fix by making `[JsonSourceGenerationOptions]` mirror MVC's `JsonOptions` exactly (the chapter's rule 1). Diff the JSON before/after in a test — that is the behavior-preservation harness this book keeps promising.

#### Interview questions

**Q: Why is source-generated JSON faster than reflection?**
A: Reflection reads type metadata at runtime (calls, allocations, no JIT-inline visibility across the boundary); source generation computes that metadata at compile time into a partial class — the serializer dispatches through pre-built converters that JIT sees as ordinary code (inlining, PGO-friendly), with no runtime type inspection. It also cuts allocations and startup, and is the only serializer mode that works under Native AOT (reflection-free requirement).

**Q: When would you NOT enable response compression?**
A: Tiny responses (<1 KB), pre-compressed content types, CPU-saturated servers (the trade tips negative), and HTTPS where BREACH-style reflected-input-next-to-secrets applies and cannot be ruled out. Also skip it when a CDN already compresses at the edge — compressing twice wastes origin CPU.

**Q: Your k6 numbers didn't improve after compression, but users report the site is faster. Explain.**
A: k6 on localhost measures server+loopback, where 12 KB vs 4 KB is ~0.1 ms; users on real networks spend transfer time proportional to bytes — that is where the win lands. The lesson: match the benchmark to the claim; for payload work, report bytes (and field measurements/CDN stats), not just localhost latency.

#### Performance checklist

- [ ] Both apps: `AddResponseCompression` (Brotli first, Fastest) + `UseResponseCompression` placed before the caching middleware
- [ ] `Content-Encoding: br` + `Vary: Accept-Encoding` verified with curl
- [ ] `AppJsonContext` generated, wired via `TypeInfoResolverChain.Insert(0, …)`; detail endpoint measured faster (X-Elapsed/alloc)
- [ ] JSON diff test proves response shape unchanged (behavior-preservation rule)
- [ ] E-09 in LAB.md names the trades: bytes −74%, CPU +0.2 ms, list endpoint deferred to Ch 16
- [ ] I can state when *not* to compress without notes

---
## Part III: Asynchronous Programming

Chapter 6 put ~60% of ShopX's time in EF Core and ~19% in JSON — those are *waiting* costs, and the machinery that decides whether waiting is cheap or catastrophic is the thread pool. Part III is the deep dive: what threads really cost (12), what `await` actually compiles to (13), and the catalog of ways to get it wrong (14) — the material that separates ASP.NET Core engineers who guess from ones who know.

### Chapter 12: Threads and the Thread Pool

#### What this chapter is about

Every request needs a worker while it executes, and — the key fact — **while a request waits on I/O, a synchronous worker waits too, doing nothing**. This chapter shows what a thread costs, how the thread pool grows (slowly — that is the trap), and then proves both with a controlled experiment on ShopX: identical endpoints, one blocking, one async, under identical load. You will watch the thread pool queue explode and then vanish.

#### A short history of this topic

Pre-2000s servers forked a process per connection; threads made that cheaper (still ~1 MB each); thread *pools* recycled them. In .NET, `ThreadPool` (2002) has had the same core design since: a global queue, per-thread local stacks with work stealing, and a governor that injects threads slowly and *experiments* (hill-climbing) to find how many the workload really wants. The async revolution (C# 5, 2012 — Chapter 2) exists precisely because pools cannot grow fast enough when thousands of requests block: `await` releases the thread for the duration of the wait, so 8 threads can carry work that would otherwise need hundreds. ASP.NET Core doubled down: no per-request SynchronizationContext (next chapter), and an *almost-everything-async* framework where blocking is the exception that must justify itself.

#### What it provides you

- The cost model: 1 MB stack per thread (default), context switches ~1–10 µs plus cache pollution, pool injection on the order of one thread per ~0.5 s when work is queued.
- The diagnostic signature: **high latency + low CPU + growing ThreadPool Queue Length = blocking**.
- A live experiment you can rerun anywhere to convince skeptics.

#### Why you should use it

Because blocking is invisible in dev (one user, LocalDB, pool never pressured) and fatal in production (peak traffic, thousands waiting, pool injecting one thread per half-second while the queue grows). It is the single most common cause of "slow in prod, fine on my machine" — and it is fully curable.

##### 12.1 What a thread costs

A managed thread is ~1 MB of reserved stack plus kernel structures plus scheduler participation. A context switch (saving registers, flipping page tables, cache pollution) costs microseconds — but the real damage at scale is *thousands of threads all waiting on I/O*: memory pressure, scheduler overhead, GC scanning bigger pointer sets. An async continuation — the state that resumes your method after `await` — is on the order of a few hundred bytes. Orders of magnitude only, but the direction is decisive: **waiting with threads is expensive; waiting with continuations is nearly free.**

##### 12.2 How the pool behaves (the part that bites)

- Work items (including `await` continuations — the *rest of your method*) go to the global queue; each worker has a local stack (LIFO, cache-friendly) and steals from others' queues when idle.
- When all workers are busy and work keeps arriving, the governor **injects** new threads — deliberately slowly (roughly one per half-second, adjusting by hill-climbing), because thousands of threads have their own cost.
- Now the failure mode: if workers are *blocked* (synchronous I/O — `Thread.Sleep`, `.Result`, sync DB drivers), the pool sees busy workers and no queue movement… but queued items pile up; injection trickles; **request latency becomes injection-limited**: seconds, not milliseconds, while CPU sits near idle. Low CPU + high latency + queue length in the thousands: that is the fingerprint.

##### 12.3 The experiment

Add `Controllers/ExperimentsController.cs` to `ShopX.Api` — two endpoints, identical waits, opposite economics:

```csharp
using Microsoft.AspNetCore.Mvc;

namespace ShopX.Api.Controllers;

[ApiController]
[Route("api/experiments")]
public class ExperimentsController : ControllerBase
{
    [HttpGet("blocking/{millis:int}")]
    public string Blocking(int millis)
    {
        Thread.Sleep(millis);                                   // 1
        return $"blocked {millis} ms";
    }

    [HttpGet("async/{millis:int}")]
    public async Task<string> Waiting(int millis)
    {
        await Task.Delay(millis, HttpContext.RequestAborted);   // 2
        return $"waited {millis} ms";
    }
}
```

- **1 — `Thread.Sleep(millis)`**: the calling thread is *held* for the whole wait. With 50 ms waits, each thread serves at most 20 requests/second — forever. 2,000 requests/second of this load *requires* ~100 threads.
- **2 — `await Task.Delay(millis, ct)`**: a timer is armed, the thread returns to the pool, and a continuation resumes the method when the timer fires. The same 2,000 rps needs *zero* dedicated threads (a handful of pool workers briefly run the continuations). `HttpContext.RequestAborted` (Chapter 5's mock reviews used the parameter form; on a `ControllerBase` this property is the same token) cancels the wait when the client disconnects.

**Load script** (`experiments.js`) — 100 virtual users hammering the *blocking* endpoint with 50 ms waits; then the same against the *async* one:

```js
import http from 'k6/http';
export const options = {
  stages: [{ duration: '30s', target: 100 }, { duration: '1m', target: 100 }],
};
const TARGET = __ENV.TARGET;                     // "blocking" | "async"
export default function () {
  http.get(`http://localhost:5080/api/experiments/${TARGET}/50`);
}
```

Run: `k6 run experiments.js -e TARGET=blocking` while watching counters:

```text
dotnet-counters monitor -n ShopX.Api --counters System.Runtime,Microsoft.AspNetCore.Hosting
```

**Blocking run (illustrative):** first seconds look fine (pool 8 threads, spare capacity), then — ThreadPool Thread Count grinds upward 8 → 12 → 20…, **ThreadPool Queue Length climbs into the thousands**, p50 ramps from ~55 ms to **1.8 s** (p95 4.2 s), while **CPU stays under 15%**. The pool is injecting ~1–2 threads/second; demand needs 100. For a full minute your users are waiting in line *while the machine idles*.

**Async run:** `k6 run experiments.js -e TARGET=async` — p50 ≈ 52 ms, p95 ≈ 61 ms, throughput ≈ 1,950 rps on 100 VUs, **Queue Length 0–3 the entire time**, Thread Count flat at ~10, CPU still low. Same machine, same 50 ms logical wait, ~35× better tail latency. That gap — not microseconds of code speed — is what `await` buys.

Put the pair in LAB.md as **E-10a/E-10b** with your own numbers; this pair is the single most convincing demo in the book, and you will reuse it in code reviews for the rest of your career.

##### 12.4 The signature table

| Observation | Blocking? | CPU-bound? | GC-bound? | Where to go |
|---|---|---|---|---|
| p95 high, CPU <20%, Queue Length 4-figures | **yes — this chapter** | no | no | Ch 14 catalog; find `.Result`/`Sleep`/sync I/O |
| p95 high, CPU ~100%, Queue 0–low | no | yes | maybe | Ch 6 profiler → Part VII / algorithm |
| Latency spikes *with* GC, alloc rate high | no | partial | **yes** | Ch 23 counters, Ch 24 allocations |
| Only one endpoint slow | isolate first (k6 tags), then decide among the above | | | |

#### Measuring the change

ShopX's normal endpoints are already async end-to-end (controllers `await` EF; EF Core is async). The experiment proves the *mechanism*, not a ShopX fix — E-10 is the evidence entry. Chapter 14 will find the one place a real system like this still blocks, and kill it.

#### Key takeaways

- Threads: ~1 MB stack, µs context switches; pool injects ~1 thread/0.5 s under queued work — never fast enough for blocking bursts.
- `await` = release the thread during waits; continuations cost ~100s of bytes. Async I/O is throughput insurance, not a micro-optimization.
- The fingerprint: high latency + low CPU + huge ThreadPool Queue Length. Memorize it; it is a production page-in-waiting.
- `Thread.Sleep` vs `Task.Delay` is the whole chapter in two method names.

#### Exercises

**Exercise 12.1 — Size the disaster.** A checkout page makes 3 sequential blocking HTTP calls of 80 ms each, at 400 rps. How many threads must exist just to hold those waits? What happens over time, and what is the fix's cost?

*Solution.* Blocking time per request = 240 ms; threads needed ≈ 400 rps × 0.24 s = **96 threads pinned to waiting**. Over time the pool crawls toward that number (~1–2 threads/s), so early minutes see multi-second queues; once at ~96 threads the app "works" but wastes ~96 MB of stacks and caps at pool size. Fix: `await` all three (releases threads; needs ~0 threads for the waits) and — Chapter 14 — run them concurrently with `Task.WhenAll` (wall time 240 ms → ~80 ms, a latency win *and* a capacity win: Little's Law, L = λ×W, both sides drop).

**Exercise 12.2 — Triage drill.** Ticket: "p95 was 900 ms at 11:02, 220 ms by 11:20, no deploy". Counters at 11:02: CPU 18%, Queue Length 6,100, Thread Count 24 rising. What happened, and what do you grep for?

*Solution.* Classic blocking burst (low CPU, giant queue, slowly growing pool) that later drained (pool finally injected enough threads / the blocking traffic receded). Grep the diff window for `.Result`, `.Wait()`, `.GetAwaiter().GetResult()`, `Thread.Sleep`, synchronous HTTP/DB calls, and any new "quick" feature that calls an external API — the Chapter 14 catalog is the checklist. The self-healing pattern (slow → fine) is the tell: pool injection *eventually* caught up.

#### Interview questions

**Q: Why does async I/O improve throughput if the work still takes the same time?**
A: Throughput is about *concurrency capacity*, not single-op speed: `await` frees the thread during waits, so a fixed pool of threads can keep thousands of waits in flight (registered with I/O completion ports/timers). Little's Law: with fixed threads L, cutting per-request thread-time W raises sustainable λ. The operation's duration is unchanged; the *thread-seconds consumed* collapse.

**Q: What does ThreadPool Queue Length mean, and what values alarm you?**
A: Work items waiting because no thread is free. Zero–low is healthy. Sustained growth — especially with low CPU — means threads are blocked (not busy computing): the pool can't inject fast enough. It is the earliest reliable signal of sync-over-async or blocking I/O in production (Chapter 29 wires it into alerts).

**Q: Explain hill climbing in one sentence.**
A: The pool's governor experiments by adding/removing threads and measures throughput response, converging on the count that maximizes completed work per second — which is why pool size drifts upward under blocking workloads instead of jumping to demand.

#### Performance checklist

- [ ] I ran E-10a/E-10b myself and have the two p95s in LAB.md
- [ ] I can recite the blocking fingerprint: high latency + low CPU + high queue length
- [ ] I grep new code for `.Result` / `.Wait()` / `Thread.Sleep` / sync I/O before review approval
- [ ] I can do the threads-needed arithmetic (rps × blocking seconds) on a whiteboard
- [ ] I understand why the pool grows *slowly* and what that does to tail latency during bursts

---
### Chapter 13: async and await From the Inside Out

#### What this chapter is about

You have used `await` fifty times by now. This chapter opens the hood: what the compiler generates, what the *fast path* is (and why it is the secret behind cheap hot paths), what happened to `SynchronizationContext` between Framework and Core (the old deadlock, the new starvation), when `ValueTask` earns its keep, and why cancellation is a performance feature. After this chapter, `await` stops being magic and becomes machinery you can reason about — and every rule in Chapter 14's catalog will make mechanical sense.

#### A short history of this topic

`await` is compiler sugar over a 1990s pattern: continuations (callbacks) with saved state. C#'s innovation (2012) was making the *compiler* do the CPS-transform — rewriting your method into a state machine — so humans write straight-line code. `ValueTask<T>` arrived in the .NET Core era (2.0-era) for hot paths where completion is usually synchronous; `IAsyncEnumerable` (C# 8, 2019) extended `await` to sequences; `WaitAsync` (2022, .NET 7) added deadline semantics. Each addition exists for the same reason: make the cheap paths cheap.

#### What it provides you

- The ability to read decompiled async code (a real skill when debugging stack traces).
- The fast-path concept: *synchronously-completed awaits cost almost nothing* — the foundation for `ValueTask`, cache-hit design, and Part V.
- Correct `ConfigureAwait`, `ValueTask`, and cancellation habits — the difference between code that passes review and code that causes incidents.

#### Why you should use it

Because every rule about async in this book is derivable from this chapter's mechanics. People who know the machinery never ask "should I await in a loop?" — they *see* what happens.

##### 13.1 What `await` compiles to

Take a minimal method (ShopX-flavored):

```csharp
public async Task<int> GetStockAsync(int id)
    => await _db.Products.Where(p => p.Id == id).SumAsync(p => p.UnitsInStock);
```

The compiler roughly generates this (simplified to the parts that matter — the real code is a sealed struct implementing `IAsyncStateMachine`, boxed once if it ever suspends):

```csharp
// ~ what the compiler writes for you:
int state = -1;                                  // -1 running; 0 = suspended at await 1; -2 = finished
TaskAwaiter<int> awaiter;
Task<int> tcs = new Task<int>();                 // the Task returned to the CALLER immediately

void MoveNext()                                  // called: first synchronously; later "when ready"
{
    if (state == 0) goto Resume;                 // we were suspended; jump to the await point
    var task = _db.Products...SumAsync(...);     // the ORIGINAL first half of your method
    if (task.IsCompleted)                        // ★ the FAST PATH
    {
        var result = task.GetResult();           // done already? continue synchronously — nothing scheduled
        state = -2; tcs.SetResult(result);       // complete the caller's Task; done. No suspension ever happened.
        return;
    }
    state = 0;                                   // mark WHERE to resume
    awaiter = task.GetAwaiter();
    awaiter.UnsafeOnCompleted(MoveNext);         // "when the DB answers, call MoveNext again"
    return;                                      // ★ the method RETURNS; the thread goes back to the pool
Resume:
    var result2 = awaiter.GetResult();           // the REST of your method (here: nothing left but return)
    state = -2; tcs.SetResult(result2);
}
```

Read it as three moments:

1. **Call**: `GetStockAsync` runs until the first `await`. It returns an unfinished `Task<int>` to the caller *immediately* — the caller's own `await` on it does the same dance one level up.
2. **Suspend** (only if the awaited thing is not already done): hoist locals into the state machine, register `MoveNext` as the continuation, `return`. **The thread is now free** — Chapter 12's entire economics.
3. **Resume**: the awaited operation completes; a pool thread calls `MoveNext`; the `goto Resume` jumps into the middle of your method; it runs to completion (or the next `await`) and completes the caller's Task.

The **fast path (★)** is the performance headline: if `task.IsCompleted` (a cache hit, an already-buffered row), the await takes the `GetResult` branch — **no state-machine scheduling, no thread handoff, near-zero overhead versus plain synchronous code**. This is why "async is slower than sync" folklore is wrong on hot paths that complete synchronously — and why the next section's `ValueTask` exists.

##### 13.2 `SynchronizationContext`: the old deadlock, the new starvation

Classic ASP.NET ran each request on a context that marshaled continuations back to *the request's thread*. Block that thread (`Task.Result`) while the continuation needs that same thread to run → both wait for each other forever: the famous deadlock. **ASP.NET Core has no per-request context**: continuations run on any pool thread. `Task.Result` therefore does not deadlock — it does something quieter: holds a pool thread for the whole wait (Chapter 12: starvation). Same bug class, different mechanism, both era-dependent — the exact trap Chapter 2 warned about.

`ConfigureAwait(false)` says "don't marshal back to a context; continue on the pool". Rules you can now *derive*:

- **Application code (ASP.NET Core)**: there is no context, so it changes nothing — omit it (noise).
- **Shared libraries** (NuGet packages, anything desktop apps might call): always `ConfigureAwait(false)` — you cannot know the consumer's context; one line protects them all.

##### 13.3 `ValueTask<T>`: the fast path, monetized

`Task` is a class: allocating one per call is a tax on hot paths. `ValueTask<T>` is a struct that wraps *either a result (no allocation) or a Task (only when truly asynchronous)*. Pattern for "usually completes synchronously" — a cache lookup with async fallback:

```csharp
public ValueTask<Product?> GetProductAsync(int id)
    => _cache.TryGetValue(id, out Product? p)         // 1
        ? new ValueTask<Product?>(p)                   // 2 — hit: zero Task allocation
        : new ValueTask<Product?>(LoadFromDbAsync(id)); // 3 — miss: real async work

private async Task<Product?> LoadFromDbAsync(int id)
    => await _db.Products.FindAsync(id);
```

- **1** — `IMemoryCache.TryGetValue` (Chapter 18) answers synchronously.
- **2** — hit path: `await GetProductAsync(...)` hits the §13.1 fast path *with no Task allocation at all*.
- **3** — miss path: the async method's Task is wrapped; costs like normal `Task` code.

The price of `ValueTask` is its contract: **await it exactly once; never store it, never await it twice, never pass it to code that might** (if a consumer needs to store/await-multiple, call `.Preserve()` once). `HybridCache.GetOrCreateAsync` returns `ValueTask` for exactly this reason (Chapter 20) — L1 hits complete synchronously.

##### 13.4 `IAsyncEnumerable<T>`: await for sequences

`await foreach` consumes elements *as they arrive* instead of materializing a whole list — the streaming answer for "don't buffer 20,000 rows to show 20":

```csharp
public async IAsyncEnumerable<Product> StreamAll()
{
    await foreach (var p in _db.Products.AsAsyncEnumerable())   // rows flow one at a time
        yield return p;                                         // consumer decides when to stop
}
```

`AsAsyncEnumerable()` (EF Core 5+) keeps the reader open and pushes rows as the network delivers them; memory stays flat regardless of table size. Chapter 16 uses it for the export endpoint; the rule: **any endpoint that could return unbounded data should stream, not `ToListAsync`**.

##### 13.5 Cancellation is a performance feature

A canceled request stops burning CPU, sockets, and database capacity the instant the user walks away. Wire tokens end-to-end:

```csharp
var source = CancellationTokenSource.CreateLinkedTokenSource(   // 1
    HttpContext.RequestAborted,
    new CancellationTokenSource(TimeSpan.FromSeconds(2)).Token);
try { var reviews = await _reviews.GetLatestAsync(productId, source.Token); }
finally { source.Dispose(); }
```

- **1 — `CreateLinkedTokenSource(a, b)`**: fires when *either* the client disconnects (`RequestAborted`) or the 2 s deadline passes — one token that means "any reason to stop".
- Prefer the built-in sugar for timeouts when you just need "abandon after X": `await something.WaitAsync(TimeSpan.FromSeconds(2), token);` (.NET 7+) — it does not cancel the underlying work (see its docs), it releases *you*; for real cancellation, the linked source above is the tool.

Audit ShopX later (Chapter 14) for token plumbing; for now the habit: **every async method signature you write from today takes a `CancellationToken` parameter with a default of `default`, and passes it down.**

#### Measuring the change

No ShopX change this chapter — the payoff lands next chapter. LAB.md **E-11**: paste the three-line version of the state-machine story *in your own words* (that is the retention test), plus the rule table: fast path / suspend / resume; no context on Core; libraries `ConfigureAwait(false)`; `ValueTask` single-await contract; tokens everywhere.

#### Key takeaways

- `await` = state machine: return a Task immediately; if not already complete, hoist state, register continuation, *return the thread*; resume on a pool thread.
- The fast path (already-complete) is nearly free — the basis of `ValueTask` (hit = no allocation) and of Part V's cache-hit economics.
- ASP.NET Core has no per-request `SynchronizationContext`: `.Result` won't deadlock, it starves (Ch 12). `ConfigureAwait(false)`: libraries yes, ASP.NET Core app code optional.
- `ValueTask` contract: await once; `Preserve()` if it must be stored.
- `IAsyncEnumerable` streams; unbounded endpoints must stream, never buffer.
- Cancellation tokens are capacity protection: link client-abort + deadline tokens and pass them down everywhere.

#### Exercises

**Exercise 13.1 — Predict the path.** For each call, say fast-path or suspend, and what the thread does meanwhile: (a) `await Task.Delay(10)`; (b) `await Task.FromResult(5)`; (c) an EF query that returns from LocalDB in 0.2 ms; (d) `await _cache.GetProductAsync(id)` on an L1 hit (§13.3 shape).

*Solution.* (a) Suspend — a real timer: thread released, resumption scheduled by the timer in 10 ms. (b) Fast path — `FromResult` is completed at creation; `GetResult` branch, no scheduling, near-sync cost. (c) Fast path in practice (awaiting a completed task) — and with `ValueTask`-returning cache APIs, zero allocation; on a `Task`-returning method the Task itself was allocated by the callee but *this* await is cheap. (d) Fast path, zero-allocation with `ValueTask` — the design point of §13.3: hot read paths hit this shape constantly.

**Exercise 13.2 — The library rule.** You extract ShopX's review fetching into `ShopX.Sdk` published on NuGet. A WPF desktop app uses it and its UI freezes. Which §13.2 rule was missed, and what exactly happens in the desktop app?

*Solution.* Missing `ConfigureAwait(false)` throughout the library. The desktop app has a `SynchronizationContext` (the UI thread); without `ConfigureAwait(false)` your continuations marshal to the UI thread — and any blocking call on the UI thread then deadlocks or queues behind UI work (the freeze). The library cannot know its consumers, so it must not assume "no context" like ASP.NET Core app code can. This is why the rule is *libraries always*; the freezing is the exact scenario the rule prevents.

#### Interview questions

**Q: What does the compiler generate for an async method?**
A: A state machine (struct implementing `IAsyncStateMachine`, boxed on first suspension): a `state` field marking the resume point, hoisted locals, and `MoveNext()` containing both halves of the method. On `await`: if completed, continue synchronously (fast path); else register `MoveNext` as the continuation and return, releasing the thread. Resumption runs the rest and completes the caller-facing `Task` via the async method builder.

**Q: Why can `Task.Result` deadlock on ASP.NET Framework but merely starve on Core?**
A: Framework marshaled continuations back to the per-request `SynchronizationContext` thread; blocking that thread on the task whose continuation needs that thread = mutual wait. Core has no such context — the continuation runs on any pool thread, so the blocked thread just *holds a thread* doing nothing (starvation under load) instead of deadlocking. Both are bugs; only the mechanism differs.

**Q: When do you reach for `ValueTask<T>`, and what do you give up?**
A: When the method usually completes synchronously (cache hits, buffered reads, first-page hits) on a hot path — the hit path then allocates nothing. You give up `Task`'s conveniences: single-await contract, no storage, no double-await, use `.Preserve()` when it must behave Task-like. For cold paths or anything stored/awaited by third parties, keep `Task` — clarity beats a micro-saving (measure first, Chapter 1's rule).

#### Performance checklist

- [ ] I can narrate call→suspend→resume and point at the fast path in the generated-code sketch
- [ ] I can explain the Framework-deadlock vs Core-starvation difference without notes
- [ ] `ConfigureAwait(false)` rule applied: libraries yes; app code optional
- [ ] New async signatures all take `CancellationToken` and pass it down
- [ ] `ValueTask` used only where sync-complete is the common case; contract respected
- [ ] E-11 state-machine story written in my own words

---
### Chapter 14: The Async Anti-Pattern Catalog

#### What this chapter is about

Chapter 12 proved blocking is fatal; Chapter 13 gave you the machinery. This chapter is the field guide: the eight ways real codebases wreck async performance, each as *broken → symptom → why → fixed*, applied to a new ShopX feature you will build now (and fix): "latest review" chips on the cart page. One sin is left in on purpose — it becomes Chapter 21's lesson.

#### A short history of this topic

Every async feature ships its own decade of misuse. `.Result` deadlocks defined Framework-era StackOverflow (2012–2016); `async void` event handlers crashed WinRT apps; "fire and forget" logging lost millions of exceptions silently; unbounded `WhenAll`s DDoS'd downstream services *from inside the company*. The catalog below is not trivia — each row has taken down real systems, and reviewers who know it on sight are the ones who prevent the next outage.

#### What it provides you

- The review checklist: eight named failure modes with their symptoms.
- The fan-out pattern (`Task.WhenAll` + ordered results + bounded parallelism) — the workhorse of Part VI.
- Another measured ShopX improvement, this time in the *MVC* app.

#### Why you should use it

Because these bugs pass every functional test. The cart page "works" in every version below — only the *time* and the *thread economics* differ, and under load the differences become outages. This is exactly the class of defect performance specialists exist to catch.

##### 14.1 The feature, naively

The product team wants the cart page to show each item's latest customer review. Reviews live behind the mock service (`/api/mock/reviews/{id}`, fixed 80 ms). First implementation — `ShopX.Web/Services/ReviewsClient.cs`:

```csharp
using System.Text.Json;

namespace ShopX.Web.Services;

public class ReviewsClient
{
    private const string ReviewsBase = "http://localhost:5080/api/mock/reviews/";

    public async Task<string?> GetLatestReviewAsync(int productId, CancellationToken ct)
    {
        using var http = new HttpClient();                                        // 1 ⚠ Ch 21's sin
        var json = await http.GetStringAsync($"{ReviewsBase}{productId}", ct);    // 2
        using var doc = JsonDocument.Parse(json);                                 // 3
        return doc.RootElement[0].GetProperty("text").GetString();                // 4
    }
}
```

- **1 — `using var http = new HttpClient()`**: disposed every call. *Functionally fine* (the response arrives), and a slow-motion socket disaster under load — leave it exactly as written; Chapter 21 dissects and fixes it. Flagging a known sin in a comment is also what senior engineers do.
- **2 — `GetStringAsync(url, ct)`**: `using var` declares + disposes at scope end; the string arrives whole.
- **3 — `JsonDocument.Parse`**: parses into a pooled buffer — `IDisposable` by design (returning the buffer); forgetting `using` here leaks pooled memory (an allocation-discipline preview of Chapter 24).
- **4 — `RootElement[0].GetProperty("text")`**: element navigation without a DTO. Quick-and-dirty for a lab; production code would use a typed DTO + source-gen (Chapter 11). We keep it raw to stay focused — this is a *parse style* decision, not a performance one.

Register it (`Program.cs`, Web): `builder.Services.AddScoped<ReviewsClient>();` — scoped for now (it owns no state; any lifetime works until Chapter 21 gives it one `HttpClient`).

Now the cart page — `CartController.Index`, first version (**sequential awaits**, sin #4 below):

```csharp
public async Task<IActionResult> Index(CancellationToken ct)        // 1
{
    var ownerId = GetOwnerId();
    var cart = await _db.Carts.FirstOrDefaultAsync(c => c.OwnerId == ownerId, ct);
    if (cart is null) return View(new List<CartItem>());

    var items = cart.Items;                                        // 2 (lazy load — Part IV's sin)
    ViewBag.Total = items.Sum(i => i.Product.UnitPrice * i.Quantity);

    var reviews = new List<string?>();                             // 3 ⚠ sequential
    foreach (var item in items)
    {
        var text = await _reviews.GetLatestReviewAsync(item.ProductId, ct);
        reviews.Add(text);
    }
    ViewBag.Reviews = reviews;
    return View(items);
}
```

- **1 — `CancellationToken ct` parameter**: in controllers it binds automatically to `HttpContext.RequestAborted` — cancellation plumbed for free (sin #6 already avoided here).
- **2** — the lazy loads stay (they are Part IV's curriculum; this chapter is about the *network* waits).
- **3** — the bug: each review call *starts only after the previous one finished*. Three items = 3 × 80 ms of serialized waiting.

Display them in `Views/Cart/Index.cshtml` (table body):

```cshtml
@for (var i = 0; i < Model.Count; i++)
{
    <tr>
        <td>@Model[i].Product?.Name</td>
        <td>@Model[i].Product?.UnitPrice.ToString("C")</td>
        <td>@Model[i].Quantity</td>
        <td class="review">@ViewBag.Reviews[i]</td>
    </tr>
}
```

`ViewBag.Reviews[i]` — dynamic indexing; list order matches `Model` order (guaranteed by the fix below). Put 3 items in the cart and measure: **X-Elapsed-Ms ≈ 412 ms** (illustrative; 240 ms serialized reviews + lazy loads + render). LAB.md **E-12a**.

##### 14.2 The catalog

| # | Anti-pattern | Symptom | One-line fix |
|---|---|---|---|
| 1 | Sync-over-async (`.Result`, `.Wait()`, `.GetAwaiter().GetResult()`) | Ch 12: low CPU, huge queue, seconds of p95 | `await` it (Core: starvation; Framework: deadlock) |
| 2 | `async void` | Exceptions vanish/crash process; caller cannot await | Return `Task` |
| 3 | Fire-and-forget (`_ = Foo()`) | Silent failures; no cancellation; on shutdown, work dies mid-flight | Await, or safe wrapper + logging |
| 4 | Sequential awaits on independent work | Latency = Σ instead of max | `Task.WhenAll` |
| 5 | `Thread.Sleep` in async code | Ch 12 economics per sleep | `Task.Delay` |
| 6 | Missing `CancellationToken` | Abandoned requests keep burning capacity | Token in, token down |
| 7 | Unbounded parallelism (`WhenAll` over 10k items) | You become the DDoS; downstream saturates | Bound with `SemaphoreSlim` |
| 8 | Library code without `ConfigureAwait(false)` | Consumer (desktop) deadlocks | `ConfigureAwait(false)` in libraries |

Four of them need code beyond the table:

**#2 — `async void` (never, outside event handlers):**

```csharp
// BROKEN — exception here crashes the process / is swallowed; caller can't know when it ends
public async void WarmCacheAsync() { await _reviews.GetLatestReviewAsync(1, default); }

// FIXED — returns a Task the caller can await, observe, or safely discard *knowingly*
public async Task WarmCacheAsync() { await _reviews.GetLatestReviewAsync(1, default); }
```

**#3 — fire-and-forget, made honest:**

```csharp
public static class TaskExtensions
{
    public static void Forget(this Task task, ILogger logger, string what)     // 1
        => task.ContinueWith(
            t => logger.LogError(t.Exception!, "Background work failed: {What}", what),
            TaskContinuationOptions.OnlyOnFaulted | TaskContinuationOptions.ExecuteSynchronously);
}
// usage:  _reviews.WarmCacheAsync().Forget(_logger, "warm cache");
```

- **1**: `ContinueWith(…, OnlyOnFaulted)` observes the task — an unobserved exception no longer disappears (it logs). `ExecuteSynchronously` avoids a thread hop. Explicit `Forget(...)` at the call site is the point: fire-and-forget becomes a *visible decision*, not an accident. On app shutdown, in-flight work still dies — the robust answer is a queue/hosted service (Chapter 29's pattern); `Forget` is for trivial best-effort work.

**#4 — the fix we apply now (fan-out):**

```csharp
var reviewTasks = items
    .Select(i => _reviews.GetLatestReviewAsync(i.ProductId, ct))   // 1
    .ToArray();                                                    // 2 — starts ALL calls now
var reviews = await Task.WhenAll(reviewTasks);                     // 3 — wall clock ≈ slowest call
ViewBag.Reviews = reviews;
```

- **1 — `Select` is lazy**: nothing has started yet (an `IEnumerable` of *intent*).
- **2 — `.ToArray()` materializes it**: every call begins now, concurrently; result order matches source order — index `i` of the array is item `i`'s review, which is what the view's `ViewBag.Reviews[i]` relies on.
- **3 — `Task.WhenAll`**: awaits all; returns `string?[]` (unwrapped — nice ergonomics); completes when the *slowest* does.

**#7 — the same fan-out, bounded** (the version you ship when "items" could be thousands — e.g., an export that calls reviews per product):

```csharp
private static readonly SemaphoreSlim Gate = new(initialCount: 4, maxCount: 4);   // at most 4 in flight

public async Task<string?[]> GetReviewsForAsync(IReadOnlyList<int> ids, CancellationToken ct)
{
    async Task<string?> One(int id)                                // 1 — local function per item
    {
        await Gate.WaitAsync(ct);                                  // 2 — enter (bounded)
        try     { return await GetLatestReviewAsync(id, ct); }
        finally { Gate.Release(); }                                // 3 — always exit
    }

    var tasks = ids.Select(One).ToArray();                         // 4
    return await Task.WhenAll(tasks);
}
```

- **1** — a local async function: cleaner than lambdas (no closure allocation for `id`).
- **2 — `Gate.WaitAsync(ct)`**: the *async* wait — a 5th caller suspends (no thread held) until a slot frees.
- **3 — `finally`**: a failed call must still release the gate, or every failure permanently shrinks capacity (a leak of *concurrency*, worse than a memory leak).
- **4**: same lazy-then-materialize trick. `initialCount = 4` is the policy: 4 concurrent calls protect the downstream service; pick the number with the downstream owner, then *measure* — Chapter 28's scenarios exist for exactly this.

For CPU-bound batches, `Parallel.ForEachAsync(source, options, async (item, ct) => …)` (in .NET 6+) is the built-in equivalent — it parallelizes with its own `MaxDegreeOfParallelism`.

##### 14.3 Measuring the fix

Cart page, 3 items, same data (illustrative):

| Version | X-Elapsed-Ms | Mechanism |
|---|---|---|
| Naive sequential (E-12a) | ~412 ms | 3 × 80 ms serialized + lazy loads + render |
| `WhenAll` fan-out (E-12b) | **~172 ms** | reviews ~85 ms (slowest call) + same lazy loads |
| Remaining cost | ~172 ms | ~90 ms lazy DB loads (Part IV), ~85 ms reviews in parallel |

LAB.md **E-12b**. Note what did *not* change: the lazy loads (Part IV's sin) and the per-call `new HttpClient()` (Ch 21's sin) — the book is deliberately sequenced; every remaining cost has a chapter with its name on it.

#### Measuring the change

E-12a → E-12b: −58% wall time on the cart page from ~6 lines changed. The headline mechanism line for LAB.md: *independent waits compose with `WhenAll` (max), dependent waits compose with sequencing (sum) — and these calls were independent.*

#### Key takeaways

- The 8-row catalog: know the symptom column by heart (it is your code-review scan pattern).
- Fan-out = lazy `Select` → `.ToArray()` (start) → `Task.WhenAll` (await max); array order = source order.
- Bound fan-out with `SemaphoreSlim` (`WaitAsync`/`finally Release`); choose the degree with the downstream owner and measure.
- `async void` never; fire-and-forget only via an explicit `.Forget(logger, …)`; libraries `ConfigureAwait(false)`.
- CancellationToken in every signature, passed down — abandonment stops costing capacity.

#### Exercises

**Exercise 14.1 — Diagnose the diff.** A pull request changes `await Foo(); ... await Bar();` to `await Task.WhenAll(FooStart, BarStart)`. What must you check before approving?

*Solution.* Independence: does Bar's input depend on Foo's result (ordering bug)? Exceptions: WhenAll surfaces the *first* failure and may cancel/duplicate side effects — are both idempotent on retry? Shared state: if both touch the same `DbContext` (not thread-safe, scoped per request — Chapter 15), parallelism corrupts it: they must use separate contexts. Capacity: two concurrent external calls where there was one — does the downstream rate limit (Ch 22)? If all clear: approve; latency becomes max instead of sum.

**Exercise 14.2 — The gate leak.** In the bounded version, a junior moves `Gate.Release()` out of `finally`, after the `await` line. Walk through the failure over 100 calls with a 5% error rate.

*Solution.* Every 20th call (5%) throws after `WaitAsync` succeeded but before `Release` — that slot is never returned. After ~20 failures (≈400 calls), all 4 slots are lost: every subsequent `WaitAsync` waits forever → endpoint hangs (p95 → timeout). Diagnosis signature: works for a while, then *all* requests to that path queue (not the whole app — scoped to this gate). `finally` is not style; it is correctness under failure.

#### Interview questions

**Q: `Task.WhenAll` vs `Parallel.ForEachAsync` — when each?**
A: `WhenAll` for *async I/O* fan-out: start tasks, await all — threads are released during waits; combine with `SemaphoreSlim` to bound. `Parallel.ForEachAsync` for *CPU-bound* (or mixed) batches: it owns a worker strategy, partitions, and honors `MaxDegreeOfParallelism` — the modern `Parallel.ForEach`. Rule of thumb: waiting → WhenAll (+gate); computing → ForEachAsync.

**Q: How do you handle "fire and forget" safely?**
A: Make it explicit and observed: a `.Forget(logger)` extension (ContinueWith/OnlyOnFaulted logs failures), or — for work that must survive — a hosted background service with a channel/queue (drains on shutdown; Chapter 29). Never bare `_ = Task…`: unobserved exceptions vanish, and app shutdown kills in-flight work unpredictably.

**Q: Why is passing `CancellationToken` a performance topic rather than politeness?**
A: Capacity. An abandoned request (user navigated away, timeout upstream) that keeps executing burns CPU, sockets, DB connections, and downstream quota for output nobody will read. At scale, disconnect rates during degradation make things worse exactly when you can least afford it (feedback loop). Tokens propagate the *stop* signal; linking them with deadlines (§13.5) gives every layer a chance to bail.

#### Performance checklist

- [ ] E-12a/E-12b measured; I can state the Σ-vs-max rule from my own numbers
- [ ] The catalog's 8 symptoms — I can scan a diff against them in under a minute
- [ ] Bounded fan-out pattern (`SemaphoreSlim` + `finally`) available in my toolkit
- [ ] `async void` count in my codebase: zero (event handlers excepted)
- [ ] Every new async method: token in, token down
- [ ] The two remaining ShopX sins have chapters with their names on them (Part IV, Ch 21)

---
## Part IV: Data Access with EF Core and SQL Server

Chapter 6 attributed ~60% of ShopX's request budget to EF Core. Part IV collects that debt: first the machinery (how a LINQ query becomes rows in your objects, and what change tracking and lazy proxies actually do — Chapter 15), then the query techniques that remove 21 of our 22 queries (Chapter 16), the SQL Server side — indexes, plans, statistics (Chapter 17), and finally a service-layer object cache for the per-user pages that response caching can never help (Chapter 18).

### Chapter 15: How EF Core Executes a Query

#### What this chapter is about

Before optimizing data access you must be able to *see* it. This chapter follows one LINQ query through EF Core's pipeline — expression tree, plan cache, SQL, materialization, change tracking — prices each stage, and turns on query logging so the rest of Part IV works with evidence instead of folklore. No ShopX changes yet except the log line; the fixes are Chapter 16's.

#### A short history of this topic

EF (2008) promised "LINQ to SQL for the enterprise" and delivered productivity at a price: change tracking by default, transparent lazy loading, and payloads that mirrored the database. EF Core (2016) was rebuilt on a compiling pipeline — LINQ *expression trees* translated per *query shape* (cached), materialization via generated code, tracking as an explicit, skippable cost. The community's hard-won defaults inverted: `AsNoTracking` for reads became standard advice, lazy loading became the smell it always was, and `.NET 7` added set-based writes (`ExecuteUpdate/Delete`). Every technique in Chapter 16 is a direct consequence of this pipeline's design.

#### What it provides you

- The five-stage cost model of every EF query (you will reuse it whenever you read an ORM's code, any ORM).
- Query logging switched on — Part IV's instrument.
- The anatomy of our 22 queries, explained mechanically.

#### Why you should use it

Because "EF is slow" is a category error — *specific stages* are expensive under *specific shapes*. People who know the pipeline say "this loop lazy-loads" and fix it in one line; people who don't say "let's try Dapper" and carry the bug into the new tool.

##### 15.1 The pipeline: five stages, priced

When you write `await _db.Products.OrderBy(p => p.Id).Skip(40).Take(20).ToListAsync()`:

1. **Expression → translation.** `_db.Products` is an `IQueryable<Product>` — the LINQ methods build an *expression tree* (code as data, Chapter 4's `HasIndex` lambda — same mechanism). EF Core's *query compiler* walks the tree and produces (and **caches by shape**) a compiled query: translation + a materializer. Cost: amortized to a dictionary lookup after the first execution of that shape.
2. **SQL execution.** One `DbCommand`, one round trip to the server. Cost: network + the *database's* work (Chapter 17's whole subject).
3. **Materialization.** For each row, run the generated materializer: allocate the entity, copy column values. Cost: per row, small but real — 20 rows are nothing; 20,000 in an export are not.
4. **Change tracking.** For each *tracked* entity: attach to the state manager and take a **snapshot** of every property value (the baseline that `SaveChanges` will diff against — `DetectChanges`). Cost: per row, comparable to materialization, and pure overhead when you never save.
5. **Identity map.** Tracked entities are also indexed by key so the same row always yields the same instance within the request (identity resolution). This is what `Attach`ing buys — and what `AsNoTracking` declines.

The 22-query anatomy of `GET /api/products?page=1` — every number now mechanical:

| # | Query | Trigger | Stage cost |
|---|---|---|---|
| 1 | `SELECT COUNT(*)` | `CountAsync()` | round trip |
| 2 | `SELECT ... ORDER BY Id OFFSET 0 FETCH 20` + **track + snapshot 20 products** | `ToListAsync()` | round trip + 2×20 row costs |
| 3–22 | `SELECT TOP(1) ... WHERE Id = @categoryId` (one per product) | JSON serializer walks `p.Category` → lazy proxy fires | **20 round trips** |

Rows 3–22 are the crime: twenty *sequential* round trips, each materializing + tracking a `Category`, triggered by *serialization*, invisible in your action code. LocalDB forgives them (~1 ms each); a remote database with 2 ms network adds 40+ ms of pure waiting, and under concurrency they multiply.

##### 15.2 What lazy proxies actually are

`UseLazyLoadingProxies()` (Chapter 4's deliberate sin) generates a *subclass* of `Product` at runtime that overrides the virtual `Category` getter: null inside? → run a query, cache the result, return it. Any code that *reads the property* pays a query: JSON serialization (API), Razor rendering (MVC), `ViewBag` math (cart totals). The proxy exists to make "just read the property" work — which is exactly why it hides in profiles as innocent-looking `SELECT TOP(1)` staircases (Chapter 6's Database timeline).

##### 15.3 Context lifetimes: `AddDbContext` vs `AddDbContextPool`

`AddDbContext` (Chapter 4): one context per request, constructed and disposed each time — cheap-ish, and the *safe default*. `AddDbContextPool` reuses context instances across requests (a pool; the context resets state on return): it removes construction cost on very hot paths. Pooling constrains what a context may do in its lifetime hooks (nothing per-request may persist) — and note that lazy-loading proxies and pooling have historically had friction combining (check the current EF docs for your version; Chapter 16 removes proxies anyway, which settles the question by making the combination moot). Rule: measure before pooling; it is a few-percent optimization, and Part IV's chapters below are worth 10×.

##### 15.4 Switching on query logging (Part IV's instrument)

One line in the `AddDbContext` options (both apps):

```csharp
builder.Services.AddDbContext<ShopXContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("ShopX"))
           .UseLazyLoadingProxies()
           .LogTo(Console.WriteLine,
                  new[] { RelationalEventId.CommandExecuted },
                  LogLevel.Information));
```

`LogTo(Console.WriteLine, events, level)` — EF's structured logging hook; `RelationalEventId.CommandExecuted` fires *after* each command with duration and the SQL. Every executed statement now prints to the console you launched the app from. Hit `GET /api/products?page=1` and count the lines with your own eyes: the count query, the page query, then twenty `SELECT TOP(1)` for categories — the Chapter 6 profiler finding, now in plain text.

One more option you will use while tuning: `.EnableSensitiveDataLogging()` prints *parameter values* (which category id, which page). Development only — parameter values can contain user data; never in production logs.

##### 15.5 The data-request cost model

For the rest of this book, evaluate any data access in these units:

> **round trips × (network latency + server work) + rows × (materialize + track) + payload bytes**

Chapter 16 attacks every term: 1 query instead of 22 (round trips), 0 tracked rows on reads (tracking), smaller DTOs (payload, compounding Chapter 11's wire win). Chapter 17 attacks the server-work term. Chapters 18–20 remove the whole formula for most requests.

#### Measuring the change

No behavior change; the instrument is live. LAB.md **E-13**: paste the actual log for one list request and label which line is which stage. From tomorrow's reviews onward, "I saw it in the command log" is the standard of proof.

#### Key takeaways

- Five stages: translate (cached by shape) → execute → materialize → track+snapshot → identity map; `AsNoTracking` skips the last two.
- Lazy proxies = runtime subclass overriding navigation getters; any property read can fire a query — serializers and views trigger them silently.
- Our 22 queries: 1 count + 1 page + 20 lazy loads (during *serialization*).
- `LogTo` + `RelationalEventId.CommandExecuted` is the cheapest profiler you own; `EnableSensitiveDataLogging` dev-only.
- Cost model: round trips × (latency + server work) + rows × (materialize + track) + payload bytes.

#### Exercises

**Exercise 15.1 — Price the move to production.** LocalDB round trip ≈ 0.5 ms; the production database sits 2 ms away. Predict the miss-path latency change for the 22-query request (server work identical).

*Solution.* Lazy loads move from 20 × ~1 ms to 20 × (2 ms network + server work) ≈ 60+ ms *just waiting*, sequential. The page query likewise. Expect the request to grow by roughly (21 extra round trips × 2 ms) + same server work — tens of ms worse — and under concurrency, the serialized waits hold pool threads (Ch 12). The deep lesson: **round trips amplify with distance**; code that is "fine" on LocalDB is the classic production surprise. Chapter 16's fix (1 query) is distance-proof.

**Exercise 15.2 — Tracking arithmetic.** An endpoint materializes 5,000 tracked rows, then never calls `SaveChanges`. What did tracking cost, and what two lines remove it?

*Solution.* Cost: 5,000 snapshots (every property copied into state entries), 5,000 identity-map insertions, GC pressure from state-entry objects — for zero benefit (no save ever diffs them). Fix: `.AsNoTracking()` on the query (skips stages 4–5), or — if the same rows appear multiple times and dedup matters — `.AsNoTrackingWithIdentityResolution()` (keeps identity map only). Roughly halves per-row EF cost on read paths; measurable in X-Elapsed and alloc/req.

#### Interview questions

**Q: Where does EF Core cache, and what exactly is cached?**
A: Compiled query *shapes*: the translation result + materializer, in a per-context-model cache keyed by the expression's shape (parameters vary, shape doesn't). Also the model itself (built once), and the service provider. Not cached: results (that is your cache layer, Part V) and SQL text per parameter values.

**Q: Why is `AsNoTracking` the default recommendation for read paths?**
A: Because tracking is write machinery: snapshot per entity + identity map, all maintained so `SaveChanges` can diff. Reads that never save pay it for nothing; `AsNoTracking` removes snapshot + identity work, cuts allocations, and avoids the accidental "same row, same instance" coupling between concurrently executing queries. Re-enable tracking only when the entities will actually be saved.

**Q: Explain the N+1 problem without using the word "queries" more than once.**
A: A loop (or a serializer, or a view) touches a navigation property N times; each touch performs one database round trip. The fix is to make the parent query fetch the children *set-based* in the same round trip — `Include` (entities) or projection (`Select` — the better default, Chapter 16). The general form: replace N dependent accesses with 1 access that returns the shape you actually need.

#### Performance checklist

- [ ] Query logging on in both apps; I counted 22 statements for the list request myself
- [ ] I can name the five pipeline stages and which ones `AsNoTracking` skips
- [ ] I can point at the exact line where lazy loads fire (serializer / Razor / ViewBag)
- [ ] E-13 in LAB.md with the labeled log
- [ ] I use the cost model (round trips / rows / payload) when reviewing any data code

---
### Chapter 16: Query Techniques That Actually Matter

#### What this chapter is about

The payoff chapter for data access. We rewrite ShopX's read paths with the four techniques that dominate real-world EF Core tuning — **projection** (`Select` into DTOs — kills N+1, tracking, *and* payload size in one stroke), **`AsNoTracking`**, **keyset pagination**, and **`AsSplitQuery`** — plus set-based writes, compiled queries, and streaming. Every change is measured. By the end, the 22-query request is a 1-query request, on the API, the MVC catalog, and the cart page.

#### A short history of this topic

These are all consequences of the Chapter 15 pipeline. Projection was always possible (`Select`) but became *the* pattern when APIs made payloads visible; `AsNoTracking` grew from a niche flag to the read-path default; keyset pagination is as old as databases (Oracle called it "rowid pagination"; the web era calls it cursor/seek pagination and every timeline you scroll uses it); `AsSplitQuery` and set-based writes (`ExecuteUpdate/Delete`) are EF Core-era answers to costs the older single-query, always-track design created.

#### What it provides you

- The projection habit: *ask the database for the shape you will actually use* — the single highest-leverage data-access change in most codebases.
- Correct pagination under depth (`OFFSET` vs keyset) and correct multi-collection loading (`AsSplitQuery`).
- The write-side tools (`ExecuteDelete/Update`) and the streaming pattern for unbounded endpoints.

#### Why you should use it

The Chapter 15 cost model, collected: projection removes 20 of 22 round trips, both per-row costs on reads, and (with Chapter 11's source-gen now unlocked for named DTOs) most of the serialization row too. One technique, four line items.

##### 16.1 Projection: the list endpoint, rewritten

New file `ShopX.Api/Models/ProductDtos.cs` — `record` is new, so word by word:

```csharp
namespace ShopX.Api.Models;

public record ProductListItemDto(                       // 1
    int Id, string Sku, string Name, decimal UnitPrice, int UnitsInStock, string CategoryName);

public record ProductListResponse(int Total, int Page, int PageSize, IReadOnlyList<ProductListItemDto> Items);

public record ProductDetailDto(
    int Id, string Sku, string Name, string Description, decimal UnitPrice,
    int UnitsInStock, string CategoryName, string CategorySlug);
```

- **1 — `public record ProductListItemDto(...)`**: a *positional record*: the compiler generates the class, init-only properties, a constructor, value equality, and `ToString` — the ideal DTO shape: immutable, cheap, no behavior. Records also serialize beautifully with source-gen.

Now `ProductsController.GetList` — note what disappeared: no lazy `Category` anywhere, no tracked entities, no anonymous wrapper:

```csharp
[OutputCache(PolicyName = "ProductsList")]
[HttpGet]
public async Task<IActionResult> GetList(int page = 1, int pageSize = 20, CancellationToken ct = default)
{
    if (pageSize is < 1 or > 100) pageSize = 20;
    page = Math.Max(1, page);

    var total = await _db.Products.CountAsync(ct);                    // 1

    var items = await _db.Products
        .AsNoTracking()                                               // 2
        .OrderBy(p => p.Id)
        .Skip((page - 1) * pageSize)
        .Take(pageSize)
        .Select(p => new ProductListItemDto(                          // 3
            p.Id, p.Sku, p.Name, p.UnitPrice, p.UnitsInStock,
            p.Category!.Name))                                        // 4 — becomes a JOIN
        .ToListAsync(ct);

    return Ok(new ProductListResponse(total, page, pageSize, items)); // 5
}
```

- **1 — `CountAsync(ct)`**: token flows into EF (its async methods all accept one — Chapter 14's rule, applied).
- **2 — `.AsNoTracking()`**: read path; skip snapshot + identity map (Chapter 15's stages 4–5).
- **3 — `.Select(...)` before `ToListAsync`**: the projection is part of the *query*, translated into the `SELECT` column list — the database returns exactly these columns, EF materializes 20 small DTOs and nothing else. (A `Select` *after* `ToListAsync` would be LINQ-to-Objects over already-materialized entities — the classic way to get none of these benefits.)
- **4 — `p.Category!.Name`**: navigation inside the projection → translated to a **JOIN** in the same statement. The N+1 is gone *structurally*: one round trip, one query. The `!` is the null-forgiving operator — telling the compiler "in valid data every product has a category"; it compiles no check.
- **5**: a **named** response type — which flips the list endpoint onto source-gen JSON. Add two lines to `AppJsonContext`:

```csharp
[JsonSerializable(typeof(ProductListResponse))]
[JsonSerializable(typeof(ProductDetailDto))]
```

Chapter 11's open item just closed itself: anonymous wrappers are gone, so the list serializes via generated code. (Behavior-preservation rule still applies: diff the JSON shape before/after — camelCase policy mirrored in §11's options.)

Same surgery on `GetDetail` (projection instead of returning the entity — one `SELECT ... JOIN Category`, no tracking, no lazy):

```csharp
[OutputCache(PolicyName = "ProductDetail")]
[HttpGet("{id:int}")]
public async Task<IActionResult> GetDetail(int id, CancellationToken ct = default)
{
    var dto = await _db.Products
        .AsNoTracking()
        .Where(p => p.Id == id)
        .Select(p => new ProductDetailDto(
            p.Id, p.Sku, p.Name, p.Description, p.UnitPrice, p.UnitsInStock,
            p.Category!.Name, p.Category!.Slug))
        .FirstOrDefaultAsync(ct);

    return dto is null ? NotFound() : Ok(dto);
}
```

##### 16.2 The MVC catalog and cart pages, same pattern

`ShopX.Web/Models/CatalogModels.cs`:

```csharp
namespace ShopX.Web.Models;

public record CatalogLine(int Id, string Sku, string Name, decimal UnitPrice, int UnitsInStock, string CategoryName);
public record CatalogPage(IReadOnlyList<CatalogLine> Items, int TotalPages, int CurrentPage);
public record CartLine(int ProductId, string Name, decimal UnitPrice, int Quantity, int UnitsInStock);
```

`CatalogController.Index` becomes the same shape as the API's `GetList` (projection → `CatalogPage`, `ViewBag` retired — the typed model carries `TotalPages`/`CurrentPage`, and the view's `@p.CategoryName` replaces `@p.Category?.Name`: **the lazy load line in the Razor is gone**). The cart page (`CartController.Index`) queries `CartItems` directly with a projection into `CartLine` (one query: name, price, stock, quantity — no `cart.Items` lazy walk, no per-item `Product` lazy loads); the reviews fan-out from Chapter 14 keys on `line.ProductId`. The views bind `@model CatalogPage` / `@model List<CartLine>` — Chapter 26 explains what that bought us (no `ViewBag` boxing, typed helpers).

##### 16.3 `Include` vs projection (when you truly need entities)

`Include(o => o.Items).ThenInclude(i => i.Product)` fetches *whole* related entities (all columns, tracked) in one query — right when you will *modify and save* those entities (a write path), or when a screen genuinely needs the full object. For read paths, projection wins every term of the cost model. Rule: **writes → tracked entities; reads → projected DTOs.**

##### 16.4 Pagination: `OFFSET` vs keyset

`Skip/Take` (current code) compiles to `ORDER BY Id OFFSET @s FETCH NEXT @n` — fine for shallow pages, but the server must walk past all skipped rows: page 5,000 pays for 5,000 rows. Keyset ("seek") pagination seeks directly:

```csharp
[OutputCache(PolicyName = "ProductsList")]
[HttpGet("since/{lastId:int}")]
public async Task<IActionResult> GetListSince(int lastId, int pageSize = 20, CancellationToken ct = default)
{
    if (pageSize is < 1 or > 100) pageSize = 20;

    var items = await _db.Products.AsNoTracking()
        .Where(p => p.Id > lastId)                       // ★ seek predicate: constant cost at any depth
        .OrderBy(p => p.Id)
        .Take(pageSize)
        .Select(p => new ProductListItemDto(
            p.Id, p.Sku, p.Name, p.UnitPrice, p.UnitsInStock, p.Category!.Name))
        .ToListAsync(ct);

    return Ok(items);
}
```

`WHERE Id > @lastId` + `ORDER BY Id` walks straight to the position — page "depth 5,000" costs the same as page 1. Cost: the caller must carry the last seen id (cursor) — that is why timelines scroll, not jump. Use OFFSET for admin grids and bounded page counts; keyset for infinite scroll and deep APIs. Requires a **stable, unique order key** (our `Id` qualifies; `CreatedAtUtc` alone does not — ties make rows skip or repeat).

##### 16.5 `AsSplitQuery`: loading collections without row explosion

One parent with many children in a single query multiplies rows: parents × children, with parent columns duplicated per child. For a *list* of parents each with collections it explodes fast (100 orders × 10 items = 1,000 wide rows before a single byte of your data). Split it:

```csharp
var order = await _db.Orders.AsNoTracking()
    .Where(o => o.Id == id)
    .Include(o => o.Items).ThenInclude(i => i.Product)
    .AsSplitQuery()                                   // 3 queries: orders; items; products
    .FirstOrDefaultAsync(ct);
```

`AsSplitQuery` issues one query per collection level. The trade (name it in review): more round trips (cost model term 1) and the levels are no longer one consistent snapshot (each query sees current data) — usually fine for reads, wrong for logic that assumes consistency. Rule of thumb: single parent + collections → split often wins; lists of parents with collections → split almost always wins; scalar/one-to-one joins → leave combined.

##### 16.6 Writes: set-based, and honest limits

Two shapes from EF Core 7 — statements, not load-modify-save:

```csharp
// Housekeeping: delete abandoned carts in ONE statement, no materialization
var cutoff = DateTime.UtcNow.AddDays(-30);
var removed = await _db.Carts
    .Where(c => c.CreatedAtUtc < cutoff)
    .ExecuteDeleteAsync(ct);                          // DELETE ... WHERE CreatedAtUtc < @cutoff

// Admin restock: one product, one UPDATE — no tracking, no round trip per row
var added = await _db.Products
    .Where(p => p.Id == productId)
    .ExecuteUpdateAsync(s => s.SetProperty(p => p.UnitsInStock, p => p.UnitsInStock + n), ct);
```

`ExecuteDelete/ExecuteUpdate` translate to single SQL statements; **no entities materialized, no tracking, no `SaveChanges` diffing** — the set-based answer for bulk writes. Honest limitation: per-row *variable* updates (like our order placement, which subtracts a different quantity per product) are still clearest as one tracked `SaveChanges` transaction — Chapter 4's `Place` stays as-is, and that is a decision, not an omission. When profiling ever shows `Place` hot, the escalation path is raw set-based SQL with a join (`FromSql`/`ExecuteSqlRaw`) — Appendix C points to the docs.

##### 16.7 Compiled queries (and why you rarely need them)

```csharp
private static readonly Func<ShopXContext, int, CancellationToken, Task<ProductDetailDto?>> CompiledDetail =
    EF.CompileAsyncQuery((ShopXContext db, int id) =>
        db.Products.AsNoTracking()
          .Where(p => p.Id == id)
          .Select(p => new ProductDetailDto(p.Id, p.Sku, p.Name, p.Description, p.UnitPrice,
                                            p.UnitsInStock, p.Category!.Name, p.Category!.Slug))
          .FirstOrDefault());
```

`EF.CompileAsyncQuery` builds the pipeline result *once* into a delegate — no shape-cache lookup per call. Modern EF Core's automatic cache makes this a micro-win (tens of nanoseconds per call) — reach for it only when a profiler proves the lookup matters (typically enormous-rps single-shape endpoints). `static readonly` field = built once per app. Note the trade: the query shape is frozen in code — a maintainability cost for a speed you must measure first.

##### 16.8 Streaming: never buffer unbounded results

```csharp
[HttpGet("export")]
public async IAsyncEnumerable<ProductListItemDto> Export(
    [FromServices] ShopXContext db, CancellationToken ct)
{
    var rows = db.Products.AsNoTracking()
        .OrderBy(p => p.Id)
        .Select(p => new ProductListItemDto(
            p.Id, p.Sku, p.Name, p.UnitPrice, p.UnitsInStock, p.Category!.Name))
        .AsAsyncEnumerable();                          // 1 — keeps the reader open

    await foreach (var row in rows.WithCancellation(ct))  // 2 — rows flow as they arrive
        yield return row;                              // 3 — JSON array streams to the client
}
```

- **1 — `AsAsyncEnumerable()`**: no `ToListAsync` — the query never materializes a list; the reader streams.
- **2 — `WithCancellation(ct)`**: composable cancellation on the sequence (§13.4/§13.5 applied).
- **3 — `yield return`**: this action *produces* elements lazily; ASP.NET Core writes the JSON array incrementally. Memory stays flat (~KB) instead of ~5 MB for 20,000 DTOs, and the client's first byte arrives while row 1 is still warm (TTFB win, Chapter 3).

Rule: **any endpoint whose result size is unbounded must stream** (exports, feeds, "all rows" admin endpoints).

##### 16.9 Measuring: the Part IV scoreboard

Illustrative, my machine, miss path (cache-busted load, Ch 9's method), same data:

| Metric | Ch 15 state | After Ch 16 | Mechanism |
|---|---|---|---|
| EF queries per list request | 22 | **1** | projection JOIN; lazy loads structurally gone |
| `/api/products` miss p95 | 471 ms | **~95 ms** | 1 round trip + 20 untracked 6-column rows |
| Allocations per request | ~215 KB | **~96 KB** | no snapshots, no proxies, source-gen JSON |
| JSON payload | 16.8 KB | **5.1 KB** (brotli ~1.5 KB) | 6 columns instead of 2 full entities |
| `/catalog` miss p95 | 540 ms | **~125 ms** | same projection in MVC |
| Cart page X-Elapsed | 172 ms | **~100 ms** | 1 query (was 4) + reviews in parallel |

LAB.md **E-14**: paste your own numbers into that table. The mechanism line to write yourself: *projection moved the work into the one place built for it — the database's SELECT list — and deleted three costs at once (round trips, tracking, payload).*

#### Measuring the change

Covered in 16.9 — including the honest ledger of what did *not* change: `Place` (write path, deliberate), the remaining ~100 ms on the cart page (reviews latency — Chapter 21's client, not EF), and server-side work inside the one query (Chapter 17's index work will cut it further).

#### Key takeaways

- Projection before `ToListAsync` = one query, no tracking, minimal payload; DTOs as positional records; source-gen flips on for named types.
- Reads: `AsNoTracking` + projection; writes: tracked entities (or set-based `ExecuteUpdate/Delete` for bulk).
- Pagination: OFFSET for shallow/admin; keyset (`WHERE key > @last`) for deep/infinite — stable unique order key required.
- Collections: `AsSplitQuery` to avoid row explosion; remember more round trips + no single snapshot.
- Unbounded endpoints stream (`AsAsyncEnumerable` + `yield return`); compiled queries only when profiling proves the shape-lookup matters.

#### Exercises

**Exercise 16.1 — Rewrite review.** Given `var products = await _db.Products.ToListAsync(); var names = products.Select(p => p.Name).ToList();` — what are the costs, and what is the one-line query replacement?

*Solution.* Costs: full-entity materialization (all columns), tracking snapshots for every row, then an *in-memory* second pass — the database shipped bytes you threw away. Replacement: `var names = await _db.Products.AsNoTracking().OrderBy(p => p.Id).Select(p => p.Name).ToListAsync(ct);` — ships one column, no tracking. Generalize: **if a `Select` sits after `await …ToListAsync()`, it is LINQ-to-Objects; move it before the await.**

**Exercise 16.2 — Pagination choice.** API requirements: (a) an admin grid, jump to any page, ≤200 pages; (b) a customer infinite scroll on 20k products; (c) a sync job that iterates the whole table nightly. Pick the pagination for each and justify.

*Solution.* (a) OFFSET — bounded depth (200 × 20 = at most 4,000 skipped rows), arbitrary jumps required. (b) Keyset — depth grows unbounded; OFFSET degrades linearly; the scroll naturally carries the cursor. (c) Keyset by PK, batched (e.g., `WHERE Id > @last TAKE 1000` in a loop) — constant cost per batch, streaming-friendly, restartable from the last id. Note (c) also wants `AsNoTracking` + projection and no per-row writes (buffer + batch `SaveChanges`).

#### Interview questions

**Q: Why does projection beat `Include` for read paths?**
A: `Include` fetches entire related entities (all columns, tracked, identity-mapped) — the database ships every column your page will never show, and EF pays tracking per row. Projection pushes the wanted shape into the `SELECT` list: fewer bytes, fewer per-row costs, no tracking — and (with DTOs) unlocks source-gen serialization. `Include` remains right for write paths that will modify and save the loaded graph.

**Q: When would you choose `AsSplitQuery` despite more round trips?**
A: When a query loads parents *with collections* — the single-query alternative duplicates parent columns across every child row (row explosion), which costs network, materialization, and memory far more than one extra round trip. Single parent + a few collections: split usually wins. Also required when one collection would push the row width over limits. Consistency caveat: split levels are separate snapshots — wrong for logic assuming one point-in-time view.

**Q: Explain keyset pagination and its constraint.**
A: `WHERE sortKey > @lastSeen ORDER BY sortKey LIMIT n` — constant cost at any depth vs OFFSET's linear cost. Constraints: the key must be *unique and stable* during pagination (else rows are skipped/duplicated — composite keys like (CreatedAt, Id) fix ties), and the client must carry the cursor, so random page jumps are out. It is the API behind every infinite scroll you have ever used.

#### Performance checklist

- [ ] All ShopX read paths: `AsNoTracking` + projection into DTOs; no lazy reads anywhere in my code (search: `?.Category`, `cart.Items`, loops over navigations)
- [ ] 1 query per list request confirmed in the EF command log (Ch 15's instrument)
- [ ] `AppJsonContext` covers every named response type; anonymous wrappers eliminated
- [ ] Deep-paging candidates identified and keyset applied where depth is unbounded
- [ ] Unbounded endpoints stream (`AsAsyncEnumerable`), memory verified flat
- [ ] E-14 scoreboard in LAB.md with before/after from my machine

---
### Chapter 17: The SQL Server Side of Performance

#### What this chapter is about

Chapter 16 fixed the *shape* of our queries; this chapter fixes the *cost of executing them*: indexes, execution plans, `SET STATISTICS IO`, SARGability, parameter sniffing, statistics. You will add a real feature (browse by category, sorted by price), watch it be slow *exactly like a production feature would be*, fix it with a covering index, and read the before/after in SQL Server's own numbers.

#### A short history of this topic

Everything here predates .NET by decades: B-tree indexes (1970s), cost-based optimization and execution plans (System R, 1970s), the `SET STATISTICS` instrumentation (SQL Server's engine grew from Sybase, 1980s). The reason this chapter exists in a .NET book: **EF Core generates the SQL, but SQL Server decides how to execute it** — and the decisions (index choice, plan caching) are invisible in C#. The ORM made data access approachable; it did not repeal physics.

#### What it provides you

- Reading `SET STATISTICS IO/TIME` and execution plans — the database's own profiler output.
- The covering-index pattern: the single most common database-side fix in web apps.
- SARGability and parameter-sniffing awareness: the two traps that make "the same query" slow in ways C# cannot see.

#### Why you should use it

Chapter 16's projection made the *statement* minimal; the server still had to find those 20 rows. "Finding rows" is where table scans, key lookups, and sorts live — the difference between 12 ms and 380 ms in this chapter's example (illustrative), all inside one perfectly-written EF query. Teams that cannot read a plan outsourced their performance to luck.

##### 17.1 Getting the SQL into a place you can run it

Two sources: (a) the EF command log (Chapter 15) — copy the `SELECT` from the console; (b) SQL Server Object Explorer (View → SQL Server Object Explorer → connect `(localdb)\MSSQLLocalDB`) — right-click the database → New Query. Paste, and before running, enable the two instruments:

```sql
SET STATISTICS IO ON;
SET STATISTICS TIME ON;
```

##### 17.2 Reading `STATISTICS IO/TIME`

Run the page query; the Messages tab shows (annotated; example output from my box — yours will differ in the numbers, not the shape):

```text
Table 'Products'. Scan count 1, logical reads 1540, physical reads 0, read-ahead reads 1518,
    lob logical reads 0, ...
Table 'Category'. Scan count 1, logical reads 3, ...

SQL Server Execution Times: CPU time = 16 ms, elapsed time = 340 ms
```

- **logical reads** — 8 KB pages read *from cache* (memory). This is the number to watch: it is deterministic (same query, same data → same number, unlike timing) and it is what index work reduces. **The unit of database cost.**
- **physical reads / read-ahead** — pages actually pulled from disk (or pre-fetched); near-zero on a warm cache — a cold-start cost, not a query-design cost.
- **Scan count** — number of starts of the accessing structure (seeks/scan partitions; >1 often means parallelism or a loop).
- **CPU time vs elapsed** — CPU doing work vs total wall clock; a large gap = waiting (locks, I/O, resources) — the same queueing lesson as Chapter 12, one layer down.

##### 17.3 The new feature (and its default cost)

Add category browse + price sort to the API list endpoint (and mirror it in MVC):

```csharp
[HttpGet]
public async Task<IActionResult> GetList(int page = 1, int pageSize = 20,
    string? category = null, string? sort = null, CancellationToken ct = default)
{
    if (pageSize is < 1 or > 100) pageSize = 20;
    page = Math.Max(1, page);

    var query = _db.Products.AsNoTracking();
    if (category is not null)
        query = query.Where(p => p.Category!.Slug == category);      // 1

    query = sort == "price"
        ? query.OrderByDescending(p => p.UnitPrice)                   // 2
        : query.OrderBy(p => p.Id);

    var total = await query.CountAsync(ct);
    var items = await query
        .Skip((page - 1) * pageSize).Take(pageSize)
        .Select(p => new ProductListItemDto(
            p.Id, p.Sku, p.Name, p.UnitPrice, p.UnitsInStock, p.Category!.Name))
        .ToListAsync(ct);

    return Ok(new ProductListResponse(total, page, pageSize, items));
}
```

- **1** — navigation in the `WHERE` → JOIN on `Categories.Slug`. Also update the Chapter 10 policy: `.VaryByQuery("page", "pageSize", "category", "sort")` — the vary set grows with the feature (Exercise 10.1's design, now real). Same for MVC's `CatalogPage` policy.
- **2** — `ORDER BY UnitPrice DESC` in SQL.

Run `GET /api/products?category=trekking&sort=price&page=1` (1,000 Trekking products — 5% of 20k, seeded by the Ch 4 modulus). Watch the EF log for the generated SQL, paste it into a query window, and measure (illustrative): **~1,540 logical reads, ~380 ms elapsed** (cold-ish), CPU 16 ms — the gap says *waiting/lookups*, not computing. Why? Look at the execution plan.

##### 17.4 Execution plans: finding the crime scene

Include the *Actual Execution Plan* button (Ctrl+M in a query window; or right-click in Object Explorer). The plan is a data-flow diagram, right to left. The operators that matter:

| Operator | Meaning | Performance verdict |
|---|---|---|
| **Clustered Index Scan** | read *every* row of the table | the whole table is your cost — the usual suspect |
| **Clustered/Nc Index Seek** | B-tree descent to matching rows | what you want |
| **Key Lookup** | for each seek hit, go back to the clustered index for missing columns | small counts fine, per-row cost at scale — the sign you need a covering index |
| **Sort** | an explicit sort run | sometimes unavoidable; a *spill* (Sort spilled to tempdb warning) is bad news |
| **Missing index hint (green box)** | SQL Server's suggestion | read it, then write it properly (below) |

Our query's plan (illustrative shape): `Index Seek` on `IX_Products_CategoryId` (Chapter 4's narrow FK index) → **Key Lookup** × every Trekking row (to get `UnitPrice` for the sort and the SELECT columns) → **Sort** → Top 20. The narrow index gave us a seek — and then charged a lookup per row plus a sort. That is the *narrow index* trap: an index that matches the `WHERE` but not the `SELECT`/`ORDER BY` buys seeks and pays lookups.

##### 17.5 The fix: a covering index

A **covering index** is one that contains everything the query touches: the seek columns, the order column, and the select list — so the query is answered entirely from the index, no lookups. SQL Server's green box suggests something close; read its parts — *equality columns first, then the order/range column, `INCLUDE` the select list* — and write it deliberately:

```sql
CREATE NONCLUSTERED INDEX IX_Products_CategoryId_UnitPrice
ON Products (CategoryId, UnitPrice DESC)
INCLUDE (Sku, Name, UnitsInStock);
```

- Key: `(CategoryId, UnitPrice DESC)` — equality column leading, sort column next: matching rows are found *already in price order* (the index rows of one category are physically sorted by price), so the Top-20 needs **no sort at all**.
- `INCLUDE (...)`: the remaining SELECT columns ride along at the leaf level (index-only scan). `Id` comes free (row locator). `Name` via the join to Categories (a small seek per row; fine).

In our lab, create it directly in Object Explorer (right-click → New Query, run the DDL). Real projects record it in a migration (and/or the fluent model: `b.Entity<Product>().HasIndex(...)` — that is the "real" home; we used `EnsureCreated`, which does not alter existing tables — noted honestly, and the DDL is equivalent).

Measure again, same query, same data (illustrative):

| | Before | After | Mechanism |
|---|---|---|---|
| Logical reads (Products) | ~1,540 | **24** | index-only; no lookups, no sort |
| Elapsed (warm) | ~380 ms | **~14 ms** | fewer pages, no waiting |
| Plan | Seek + 1,000 Key Lookups + Sort | **Seek (ordered) + Top** | sort eliminated structurally |

That is a **27× query-time reduction from one DDL statement** (illustrative) — the standard result size for "first covering index on the hot path", and why Chapter 30's audit method checks indexes before touching C#. Note what did *not* change: the C#, the EF query, the DTOs. One database object. **LAB.md E-15.**

##### 17.6 SARGability: predicates the index can use

An index can serve a predicate only if the *column stands alone* on one side:

```sql
WHERE Name LIKE 'Alpine%'        -- seekable (prefix): range within the index order
WHERE Name LIKE '%Pole'          -- scan: leading wildcard — no starting point in the B-tree
WHERE UPPER(Name) = 'ALPINE...'  -- scan: the column is inside a function
WHERE YEAR(CreatedAtUtc) = 2025  -- scan; use a range instead:
WHERE CreatedAtUtc >= '2025-01-01' AND CreatedAtUtc < '2026-01-01'   -- seekable
```

The EF translation of `p.Category!.Slug == category` is SARGable (plain equality). Watch for LINQ that wraps columns in methods (`p.Name.ToUpper() == …`, `p.CreatedAtUtc.Year == …`) — they compile, run, and silently forfeit every index. Leading-wildcard *search* (the classic product-search box) is not fixable with a B-tree at all: that is what Full-Text Search (built into SQL Server) or an external search engine exists for — a deliberate architecture decision, not an indexing tweak.

##### 17.7 Parameter sniffing: the same query, different speeds

The Chapter 15 pipeline caches query plans *per shape* — SQL Server caches its plan *per shape + first-seen parameter values* ("sniffing"). Cached plan built for `@category = 'sailing'` (40 rows) may be a scan that is fine for 40 rows and terrible for `trekking` (1,000); it flips after a restart or plan flush, and support says "it was fast yesterday". Fixes (awareness level — coordinate with DBAs): `OPTION (RECOMPILE)` for the rare-statement case, `OPTIMIZE FOR UNKNOWN` for skewed-parameter cases, or shape-separation (different query text per case). The diagnostic habit: when "the same query" has different speeds by parameter value, *think sniffing before blaming the server*.

##### 17.8 Statistics and maintenance (the quiet part)

The optimizer prices plans using **statistics** — histograms of column value distributions, auto-created with indexes and auto-updated when rows change enough (historically ~20% + 500 rows; modern CE is more aggressive). Out-of-date statistics = confidently wrong plans. Index **fragmentation** (page splits from random inserts) grows logical reads slowly. Both are why production databases run maintenance jobs — the community standard is Ola Hallengren's scripts (free; see Appendix C). For the lab, know that they exist; for your career, know that "suddenly slow in a table that grew past a threshold" is often *statistics*, not code.

#### Measuring the change

E-15 as in 17.5. The list endpoint's default (no category) path is unchanged (~95 ms — the `ORDER BY Id` path was already covered by the clustered PK; say so: not every query needs a new index — the *hot shapes* do).

#### Key takeaways

- `SET STATISTICS IO/TIME`: logical reads = deterministic cost unit; CPU vs elapsed gap = waiting.
- Plans: seek good, scan suspect on hot paths, key lookups = "needs covering", spilled sorts are incidents.
- Covering index: equality columns → order column → `INCLUDE` select list; it can remove lookups *and* the sort structurally.
- SARGability: keep columns bare in predicates; leading-wildcard search needs FTS/search engines, not B-trees.
- Parameter sniffing: same query, different speeds by value — check plan reuse before blaming hardware.
- Statistics/fragmentation: the slow-burn costs behind maintenance jobs; grow past a threshold and plans quietly rot.

#### Exercises

**Exercise 17.1 — Index the order path.** The order-detail screen loads an order + items (Ch 16's split query). Which index would you propose, and what must be in `INCLUDE`?

*Solution.* The query: `WHERE OrderId = @id` on OrderItems, selecting ProductId, Quantity, UnitPrice. Index: `IX_OrderItems_OrderId ON OrderItems(OrderId) INCLUDE (ProductId, Quantity, UnitPrice)` — Chapter 4's `HasIndex(oi => oi.OrderId)` was the narrow version (key lookups per item); the covering version answers from the leaf alone. The `Items.Product` join then seeks Products by PK per item (already optimal). General recipe: equality key first, everything the screen shows in `INCLUDE`.

**Exercise 17.2 — Sniffing drill.** A report is fast for `region = 'EMEA'` and slow for `'APAC'`, after a restart it inverts. Explain, and give two mitigations with their trade-offs.

*Solution.* Sniffed plan cached from whichever region ran first; one region's cardinality (row counts) suits a seek+lookup plan, the other's suits a scan. Restart/flush re-caches from the next-seen value, inverting the luck. Mitigations: `OPTION (RECOMPILE)` — fresh plan per execution at CPU cost per run (good for rare, expensive reports); `OPTIMIZE FOR UNKNOWN` — plan for average density, stable but never ideal for either extreme (good for uniform-ish data). Shape-separation (separate query text per region) trades one cached plan for two, each sniffed on its own values.

#### Interview questions

**Q: What is a covering index and when does it stop being worth it?**
A: One that includes all columns a query touches, so it is answered from the index without key lookups. It stops paying when: the INCLUDE list approaches the row width (you are duplicating the table), write amplification hurts (every index column must be maintained on INSERT/UPDATE), or several hot queries want *different* covers — then pick the hottest few and let the rest use lookups. Always: measure logical reads before/after; the index must earn its write cost.

**Q: Logical vs physical reads — which do you optimize against, and why?**
A: Logical reads (pages from cache): deterministic, reproducible, and the ceiling of physical cost (a cold run can do at most as much disk I/O). Physical reads are a warm-up artifact. Optimizing logical reads is optimizing the plan; chasing physical reads on a cold cache mostly measures your buffer pool.

**Q: Your EF query is perfect (projection, no tracking) but still slow. Where do you look?**
A: The execution plan and STATISTICS IO: likely a scan or key-lookup storm on a hot shape (covering index), a spilled sort (order column in the index), a non-SARGable predicate the C# hid (functions/wildcards), or parameter sniffing (per-value timings). The C# being right is necessary, never sufficient — the database still gets a vote.

#### Performance checklist

- [ ] I ran STATISTICS IO on the category query and read logical reads/CPU/elapsed myself
- [ ] The covering index created; before/after (reads, ms, plan shape) in LAB.md E-15
- [ ] Output-cache policies updated for category/sort (vary set grew with the feature)
- [ ] I audited my queries for SARGable predicates (no functions/wildcards on columns)
- [ ] I know the sniffing symptom (per-value timing swings) and its two mitigations
- [ ] Maintenance reality noted: statistics + fragmentation are production's slow burn

---
### Chapter 18: Caching Database Results with IMemoryCache

#### What this chapter is about

Output caching (Chapter 10) caches *responses* — perfect for public GETs, useless for per-user pages (carts) and for data shared *behind* several endpoints. The service layer is where those live, and `IMemoryCache` is the built-in tool for it. This chapter teaches the mechanics (entries, expiration, size, eviction, versioned-key invalidation), the hit-ratio metric, and the stampede problem with a live lock demo — the exact machinery `HybridCache` will give you for free in Chapter 20.

#### A short history of this topic

.NET Framework had `System.Runtime.Caching.MemoryCache` (2010, a whole AppDomain-scoped cache with policies). ASP.NET Core shipped `Microsoft.Extensions.Caching.Memory` (2016): a much smaller, thread-safe `IMemoryCache` — a dictionary of entries with expiration and (optional) size accounting, integrated with DI and logging. It is deliberately minimal: no tags, no enumeration, no distribution — which is why patterns (versioned keys, double-checked locking) and successors (HybridCache) exist. Every pattern below is something teams hand-rolled for years; you will write them once so Chapter 20's one-liners make sense.

#### What it provides you

- `IMemoryCache` fluency: `GetOrCreate`, entry options, `SizeLimit` + compaction, per-key locking (single-flight), versioned-key invalidation.
- The hit-ratio metric (the number that tells you if your cache is working).
- A live stampede experiment — the demo that convinces rooms.

#### Why you should use it

Because the first cache most real systems need is *this* one: small shared lookups (categories, settings, feature flags) feeding many requests and many endpoints. And because every mistake in this chapter (unbounded growth, stampede, stale-forever entries) is a production incident you will now recognize on sight.

##### 18.1 Placement: which layer caches what

| Layer | Tool | Caches | Right for |
|---|---|---|---|
| Response | OutputCache (Ch 10) | whole responses | public GETs |
| **Service / domain** | **IMemoryCache (this chapter)** | objects your code reuses | shared data behind many endpoints; per-user pages' *shared* parts |
| Distributed | Ch 20 | shared across processes/servers | scale-out |

The rule that keeps you safe: **cache shared data in shared caches; per-user data only in per-request or carefully-keyed per-user stores** — a cart's *product rows* are shared (cacheable); the cart's *contents* are per-user (do not put in a global cache under one key).

##### 18.2 ShopX application: a cached category lookup

New endpoint `GET /api/categories` (20 rows, immutable after seed — the classic first service-cache). `ShopX.Api/Services/CategoryService.cs`:

```csharp
using Microsoft.Extensions.Caching.Memory;
using ShopX.Data;
using ShopX.Data.Entities;

namespace ShopX.Api.Services;

public class CategoryService(IMemoryCache cache, ShopXContext db)          // 1
{
    private const string VersionKey = "catalog:version";
    private const string KeyPattern = "catalog:categories:v{0}";

    public async Task<IReadOnlyList<Category>> GetCategoriesAsync(CancellationToken ct)
    {
        var key = string.Format(KeyPattern, GetVersion());                  // 2

        return await cache.GetOrCreateAsync(key, async entry =>
        {
            entry.AbsoluteExpirationRelativeToNow = TimeSpan.FromMinutes(30);
            entry.Size = 1;                                                 // 3
            return await db.Categories.AsNoTracking()
                .OrderBy(c => c.Name)
                .ToListAsync(ct);
        });
    }

    private int GetVersion() => cache.TryGetValue(VersionKey, out int v) ? v : 1;

    public void Invalidate()                                               // 4
        => cache.Set(VersionKey, GetVersion() + 1, new MemoryCacheEntryOptions
               { AbsoluteExpirationRelativeToNow = TimeSpan.FromHours(8) });
}
```

- **1 — `public class CategoryService(IMemoryCache cache, ShopXContext db)`**: a **primary constructor** (C# 12): parameters are declared inline and stay in scope for the whole class — the compiler writes the field capture and constructor for you. One construct, four words saved; you will see it everywhere in modern .NET.
- **2 — versioned keys**: `IMemoryCache` cannot enumerate or evict by prefix — so *invalidation* works by **making old keys unreachable**: every key embeds a version number; `Invalidate()` bumps the version, and all old-version entries become garbage that expires naturally. The poor man's tags — and the exact idea OutputCache gives you natively in Chapter 10.
- **3 — `entry.Size = 1`**: one cost unit per entry. Meaningful only when the cache was registered with a `SizeLimit` (below); required in that mode (an entry without size throws — a deliberate API choice forcing you to think about cost).
- **4 — `Invalidate()`**: the write path calls this after data changes. In ShopX, product/category rows are immutable after seed, so this is defensive — the *mechanism* is the lesson; in real systems prices and names change, and this method is what keeps the cache honest. Note the version entry itself gets a long TTL so it does not evaporate mid-day.

Register (Api `Program.cs`):

```csharp
builder.Services.AddMemoryCache(o => o.SizeLimit = 2_000);   // 1
builder.Services.AddScoped<CategoryService>();
```

- **1 — `SizeLimit = 2_000`**: the cache may hold ~2,000 cost-units before it **compacts** (evicts) under pressure. Without a limit, `IMemoryCache` grows until the process suffers (Chapter 23's gcdump will show you the aftermath). The number is a budget, decided with the memory chapter's data — 2,000 entries of ~1–3 KB is ~5 MB, fine for our box; *record yours in LAB.md*.

The controller (primary constructor again; note: **no** `[OutputCache]` here — deliberately: this endpoint demonstrates the *service* layer; in apps where response caching covers an endpoint, the response layer will absorb most traffic and this layer serves output-cache misses and internal callers):

```csharp
[ApiController]
[Route("api/[controller]")]
public class CategoriesController(CategoryService categories) : ControllerBase
{
    [HttpGet]
    public async Task<IActionResult> GetList(CancellationToken ct)
        => Ok(await categories.GetCategoriesAsync(ct));
}
```

##### 18.3 Measuring a cache: hit ratio

A cache is working iff most reads hit. Instrument it (this is Chapter 29's `System.Diagnostics.Metrics`, met early):

```csharp
private static readonly Meter Meter = new("ShopX.Api");                          // 1
private static readonly Counter<long> Hits = Meter.CreateCounter<long>("shopx.cache.category.hits");
private static readonly Counter<long> Misses = Meter.CreateCounter<long>("shopx.cache.category.misses");
```

- **1 — `new Meter("ShopX.Api")` / `CreateCounter<long>(...)`**: named counters any listener (dotnet-counters, OTel) can observe. Increment in the service: `Hits.Add(1)` on the hit path, `Misses.Add(1)` in the factory. **Hit ratio = hits ÷ (hits + misses)** — target >90% for hot lookups; a ratio that hovers at 50% means your TTL is shorter than your traffic's memory (Chapter 19 tunes this properly).

##### 18.4 The stampede, live

The failure mode: a popular entry expires; 100 requests arrive in the same instant; *all 100* miss and *all 100* run the expensive factory concurrently — the cache collapses exactly when it is needed (the "thundering herd"). Demonstration endpoint in `ExperimentsController` — two modes, same 200 ms "expensive" computation, 1-second TTL:

```csharp
[HttpGet("cache/{mode}")]
public async Task<IActionResult> CacheDemo(string mode, CancellationToken ct)
{
    const string key = "demo:expensive";

    if (_demoCache.TryGetValue(key, out int value))                  // 1 — outside the gate
        return Ok(new { value, source = "cache" });

    if (mode == "lock") await _gate.WaitAsync(ct);                   // 2 — at most 1 inside
    try
    {
        if (_demoCache.TryGetValue(key, out value))                  // 3 — re-check inside
            return Ok(new { value, source = "cache" });

        await Task.Delay(200, ct);                                   // 4 — the expensive work
        value = Environment.TickCount;
        _demoCache.Set(key, value, TimeSpan.FromSeconds(1));
        return Ok(new { value, source = "computed" });
    }
    finally { if (mode == "lock") _gate.Release(); }                 // 5
}
```

(with fields `private static readonly IMemoryCache _demoCache = new MemoryCache(...); private static readonly SemaphoreSlim _gate = new(1,1);`)

- **1 — check outside the gate**: the 99% case (entry present) never touches the lock.
- **2 — the gate**: the *first* miss enters; concurrent misses suspend at `WaitAsync` (no threads held — Chapter 12 economics).
- **3 — double-check inside**: the waiters wake, re-check, and now find the entry filled by the winner — one computation total.
- **4/5 — work + `finally Release`** (Chapter 14's gate-leak rule).

Burst it with k6 (100 VUs, 5 s): **mode `nolock`** — ~90+ `computed` responses per expiry second (illustrative: 93 computed / 7 cached); **mode `lock`** — 1 computed, 99 cached. Same cache, same TTL, same workload; the only variable is single-flight. This is E-16's centerpiece; LAB.md gets both counter screenshots. (You met this idea already: OutputCache's built-in locking, Chapter 10. `HybridCache` gives it to you at every layer, Chapter 20.)

##### 18.5 Eviction semantics (what actually happens inside)

- `AbsoluteExpirationRelativeToNow` — dies at a fixed time; the default choice for reference data.
- `SlidingExpiration` — dies after unused for X; good for *large, cold-ish* sets (auto-forgetting), dangerous for hot sets (a steadily-used entry never expires — staleness forever).
- Expired entries are removed lazily: on access, or when the cache **compacts** under `SizeLimit` pressure. Memory between those moments includes dead-but-present entries — do not be surprised by `dotnet-counters` heap readings.
- Compaction picks by cost/age; the `Size` you assign is the currency. An entry with `Size` unset throws under `SizeLimit` (§18.2's point 3) — the API refuses to guess.

#### Measuring the change

| Metric (illustrative) | Before | After |
|---|---|---|
| `GET /api/categories` p95 | ~18 ms (20 rows from LocalDB) | **~0.3 ms** on hits |
| EF queries on hit | 1 | **0** |
| Stampede demo (100 VUs/expiry) | 93 computations | **1** (mode `lock`) |

Honest ledger: our heavy endpoints are already covered at the response layer (Ch 10) — this chapter added the *pattern* on a fresh endpoint, the metric, and the demo. In a codebase without output caching (most pre-.NET 7 code you will inherit), this layer is the main event. LAB.md **E-16**.

#### Key takeaways

- Layer rule: responses → output cache; shared domain data → service cache; per-user data → per-request/per-user stores, never one global key.
- `IMemoryCache`: `GetOrCreate` + entry options; `SizeLimit` + `Size` = a budget; without a limit it grows until it hurts.
- Invalidation without tags: versioned keys — bump the version, old entries become unreachable garbage.
- Hit ratio is the cache's report card (instrument with `Meter`/`Counter`); tune TTL against it.
- Stampede: concurrent misses multiply work exactly at peak; single-flight via double-checked gate (or OutputCache/HybridCache built-ins).

#### Exercises

**Exercise 18.1 — Budget the cache.** You cache 5,000 product DTOs (~1.5 KB each) with `Size = 1` and `SizeLimit = 5_000`. What is the real memory cost, and what happens at 5,001?

*Solution.* Real cost: 5,000 × ~1.5 KB entities + entry/object overhead (~100–200 B each) + dictionary slots ≈ **8–9 MB, not 7.5** — overhead is part of budgeting. At 5,001 the cache does not refuse: it compacts (evicts) to make room — silently. Your hit ratio, not your code, tells you: if compaction is evicting hot entries (ratio drops), raise the limit or shrink what you cache (Ch 16: cache the DTO, not the entity — already done here); if memory is the constraint, the limit *is* the feature.

**Exercise 18.2 — TTL design.** Categories: immutable in practice. Cart contents: per-user, changes on every click. Reviews: external, 80 ms, tolerably stale for minutes. Assign layers + TTLs + invalidation triggers.

*Solution.* Categories: service cache, TTL hours (or version-invalidation on admin edits) — staleness cost ≈ 0. Cart contents: *not* a cache — it is state; live per-request queries (already one lean query after Ch 16) — a cache here would serve *wrong carts* (correctness, not perf). Reviews: response or service cache per product, TTL minutes (5–15), invalidate on new review via versioned key `reviews:v{n}:{productId}`; stampede protection mandatory (Ch 20's HybridCache gives it). The meta-lesson: TTL is a product decision per data type — record each in LAB.md like Chapter 10's catalog decision.

#### Interview questions

**Q: Why does `IMemoryCache` not support enumeration or prefix eviction, and what do you do instead?**
A: By design: exposing enumeration invites race-prone snapshots and locks the implementation; the team chose a minimal, thread-safe dictionary. Instead: versioned keys (bump a generation number; old entries become unreachable and expire), or tags at a different layer (OutputCache tags, Ch 10; HybridCache tags, Ch 20). Hand-rolled prefix scanning under a lock is the anti-pattern to refuse in review.

**Q: What is a cache stampede and what are the three standard defenses?**
A: Expiry + concurrent load → N simultaneous expensive fills, worst exactly at peak. Defenses: (1) single-flight — one filler, waiters reuse (double-checked lock; built into OutputCache/HybridCache); (2) early/jittered refresh — refresh before expiry, spread over time so misses never align (Ch 19); (3) request coalescing at the edge (CDN/proxy holds requests). Plus the honest fourth: *let it happen* when the fill is cheap — measure first.

**Q: When is `SlidingExpiration` the wrong choice?**
A: For data that must be fresh eventually: a hot entry keeps being touched, sliding never fires, and the value lives forever (staleness unbounded). Use absolute (or absolute + short sliding as a cap). Sliding fits large, rarely-changing *cold* sets where you want to forget unused entries gracefully.

#### Performance checklist

- [ ] `AddMemoryCache(SizeLimit=…)` — a number I can defend; every entry sets `Size`
- [ ] Versioned-key invalidation in place for the service cache; write path calls `Invalidate()`
- [ ] Hit/miss counters live (Meter); ratio visible under load
- [ ] Stampede demo run in both modes; numbers in LAB.md E-16
- [ ] Per-user data is never stored under a single shared key (cart rule)
- [ ] TTL decisions recorded per data type with the business staleness tolerance

---
## Part V: Caching Architecture

Chapters 9, 10 and 18 gave you three caching layers in isolation. Part V is the system view: Chapter 19 builds the strategy (patterns, expiration design, invalidation, stampede theory, and the policy table that turns opinions into decisions), and Chapter 20 replaces the hand-rolled machinery with `HybridCache` — and explains the distributed world (Redis) conceptually, with no infrastructure required.

### Chapter 19: Caching Strategy and Patterns

#### What this chapter is about

A cache is easy to add and easy to get wrong: stale data leaks, memory grows without limit, expiries synchronize into stampedes, and per-user data ends up in shared keys. This chapter is the design layer above the APIs you already know: the canonical patterns, how to choose TTLs and invalidation per data type, the mathematics of stampedes, and the decision tree you will apply to every "should we cache this?" question for the rest of your career. No new ShopX code — this is the chapter you re-read before designing any cache.

#### A short history of this topic

The patterns predate the web: **cache-aside** is how CPU caches and file systems always worked (ask, miss, fill, return); **write-through/write-behind** came from database buffer pools (1970s–80s). The web era added the failure modes: memcached (2003) popularized large shared in-memory tiers, and with them the *thundering herd* (a term borrowed from networking) — and CDNs turned `Cache-Control` headers into a global market. Every pattern below has a twenty-year production track record; treat blog posts about "new caching strategies" with Chapter 1's discipline — the physics has not changed.

#### What it provides you

- The pattern table (aside / read-through / write-through / write-behind) with honest trade-offs.
- Expiration + jitter, invalidation strategies, and the stampede math.
- The **data-type policy table** — the single most reusable artifact in Part V.

#### Why you should use it

Because caching is the highest-leverage optimization (it deletes work entirely, Chapter 7) *and* the highest-risk one (it deletes *freshness*, which is a correctness property). Strategy is what keeps the second from eating the first.

##### 19.1 The levels, and what each is for

```
Browser cache      ~0 ms   per user, per device       — repeat views (Ch 9)
  ↓ miss
CDN / proxy        ~10–50 ms region                   — public, non-personalized, hot
  ↓ miss
Output cache       ~0.4 ms in-process, per endpoint   — public GETs (Ch 10)
  ↓ miss
Service cache      ~0.05–0.3 ms in-process, shared    — domain data (Ch 18/20)
  ↓ miss  (L2, if deployed: ~0.5–2 ms, shared across processes — Ch 20)
Database           5–200 ms+ per query               — the truth
```

Each level exists because the one above it is too slow or too shared. Traffic that misses *all* levels pays full price; traffic that hits any level never knows the levels below exist. **Latency numbers are orders-of-magnitude rules of thumb** (Chapter 3) — measure your own.

##### 19.2 The four patterns

| Pattern | Flow | You write | ShopX usage | Watch out for |
|---|---|---|---|---|
| **Cache-aside** (lazy) | read cache → miss → read source → fill cache → return | the read + fill + invalidation (Ch 18's `CategoryService`) | everything we cache | stampede on expiry; staleness window = TTL |
| **Read-through** | app asks cache; cache itself loads on miss | nothing (the cache is a component) | our L2 story in Ch 20 approximates this | a product feature, not an option you pick |
| **Write-through** | write source *through* the cache; both updated together | dual-write logic | — (we invalidate instead) | write latency = both stores; partial failure needs handling |
| **Write-behind** | write cache, return; cache persists to source later | queueing + durability | never for money paths | data loss window on crash — forbidden for orders/stock |

ShopX's choice everywhere: **cache-aside + write-invalidate** (write commits → evict → next read refills). It is the simplest correct pattern; the others earn their complexity only at write-heavy scale.

##### 19.3 Expiration design

- **Absolute TTL** as the default (staleness is bounded). Sliding only for cold, large sets (Ch 18.5's trap).
- **Jitter**: if 10,000 entries get the same 10-minute TTL at deploy time, they expire *together* — one synchronized stampede (E-16 proved the shape). Spread expirations: `TTL = base ± random(±10%)`. One line of code, removes the alignment.
- **TTL is the backstop, not the plan** (Ch 10's rule, generalized): plan invalidation for every entry whose staleness has a business cost; TTL bounds the damage when events are lost.

##### 19.4 Invalidation, ranked by freshness need

1. **Event/tag invalidation** (write commits → `RemoveByTagAsync` / version bump): minutes-fresh data whose staleness is visible to users (prices, stock *display*, lists).
2. **Versioned keys** (Ch 18) where tags are unavailable: same effect, more ceremony.
3. **TTL-only**: data whose consumers tolerate bounded staleness (reviews, category counts). Cheapest; never for anything money-adjacent.
4. **No cache at all**: checkout, cart totals computed at charge time, per-user authorization decisions. Not caching is a *design decision*, and it is frequently correct — put it in the policy table so nobody "optimizes" it later by accident.

##### 19.5 Stampede mathematics

If a key serves R requests/second with TTL T and refill cost C (seconds), then at expiry you get up to **min(R × C_delay, concurrent arrivals during fill)** simultaneous fills (E-16 measured it: 93 vs 1). Defenses, in order of preference:

1. **Single-flight** (one filler; waiters reuse) — built into OutputCache and HybridCache; hand-rolled with the double-checked gate (Ch 18.4) elsewhere.
2. **Jittered/early refresh** — proactively refresh just before expiry, staggered, so misses never align. (Research has fancy variants; the engineering version is: refresh at 80–90% of TTL if touched.)
3. **Accept it** — if refill is 5 ms and traffic is low, a stampede of two is not a problem. Measure before building (Ch 1, always).

##### 19.6 The dangers checklist

- **Stale reads**: bounded by TTL *by design decision*, per data type — write the bound down.
- **Unbounded growth**: every cache needs a budget (SizeLimit/eviction policy, or an external cache's maxmemory + `allkeys-lru`).
- **Hot keys**: one key absorbing a majority of traffic — fine in-process; in a distributed tier it concentrates load on one shard (Chapter 20's topology note).
- **Serialization cost**: in-process caches share *objects*; cross-process caches ship *bytes* — every L2 hit pays serialize/deserialize (this is why L1 exists: Chapter 20's two-tier design).
- **Per-user data in shared keys**: the leak rule (Ch 10/18) — correctness, not performance.
- **Cache-as-database**: if the system cannot recover from an empty cache (cold start = outage), the design is wrong — a cache must degrade to "slower", never to "broken".

##### 19.7 The decision tree

```
Should this data be cached?
├── Is it per-user/personalized? ── yes ──► only per-request or per-user-keyed; never one shared key
├── Is it money/checkout-accurate? ── yes ──► display cache OK if labeled; truth always from source at transaction time
├── Does staleness have a visible business cost? ── yes ──► event/tag invalidation + short TTL backstop
├── Is it read-heavy and shared? ── yes ──► cache-aside at the highest layer that can serve it
└── otherwise ──► measure; cache only if the number says so
```

##### 19.8 ShopX's policy table (the artifact to copy)

| Data | Layer(s) | TTL | Invalidation | Staleness tolerated |
|---|---|---|---|---|
| Category list | Service (Ch 18/20) | 30 min | version/tags on admin edit | hours — cosmetic names |
| Product list/detail | Output cache (Ch 10) | 2/5 min | `catalog` tag on order | minutes — stock display |
| Product price shown | same | same | same | minutes (price never changes in ShopX — recorded as *decision*) |
| Stock at checkout | **never cached** — read at charge time | — | — | zero |
| Reviews | Response/service | 5–15 min | versioned key on new review | minutes |
| Cart contents | **not cached** — live state | — | — | zero (correctness) |
| Health/metrics | never cached | — | — | zero |

This table *is* the design. When someone proposes "let's cache the cart page" in a meeting, you do not argue — you point at the row and ask which cell they propose changing, and what the new staleness bound is.

#### Measuring the change

No code this chapter; the measurement is the review: walk the danger checklist against Chapters 9/10/18's ShopX caches and confirm every row of the policy table is defensible. Any row you cannot defend gets fixed *now*, in minutes, instead of in production, in an incident.

#### Key takeaways

- Levels: browser → CDN → output → service/L2 → database; hits at any level never reach the levels below.
- Cache-aside + write-invalidate is the default pattern; write-through/behind earn complexity only at write-heavy scale (and never for money paths).
- Expiration: absolute + jitter (never synchronized TTLs); TTL is the backstop, invalidation is the plan.
- Stampede: single-flight first (built into OutputCache/HybridCache), early refresh second, measure before over-engineering.
- Policy table per data type (layer/TTL/invalidation/staleness bound) — the artifact that survives design meetings.

#### Exercises

**Exercise 19.1 — Policy table for a new domain.** Write the table for a flight-search site: results page, price at booking, seat map, user profile, "flights under $200" promo list.

*Solution.* Results page: response/output cache, TTL 1–5 min, evict on schedule change; staleness = minutes (inventory shifts constantly, tolerable). Price at booking: **never cached** — quoted live at transaction; the *displayed* price may be cached with a "refreshed at" timestamp. Seat map: service cache TTL minutes, invalidate on check-in/assignment events. User profile: per-request; edits write through the source. Promo list: service cache, TTL hours, invalidate on merchandising publish — staleness hours by business decision. The pattern to internalize: **freshness demand is a property of the data, not of the technology** — the same Redis serves all rows above differently.

**Exercise 19.2 — Stampede sizing.** A key serves 500 rps; refill takes 300 ms; TTL 60 s. How many refills/hour without protection? With single-flight? With jitter + early refresh at 90%?

*Solution.* Unprotected: every expiry opens a window of ~300 ms during which arrivals (500 × 0.3 = up to 150 requests) can all start fills — say K ≤ 150 fills per expiry; 60 expirations/hour → up to ~9,000 refills/hour, each 300 ms of duplicated source load. Single-flight: 60 refills/hour (one per expiry). Early refresh at 90% + jitter: refills happen *before* expiry, off the request path (a scheduled task or the first arrival after 54 s) — source never sees a burst; count ≈ 60/hour, but latency impact ≈ 0. The numbers are the argument: protection is cheaper than the incident.

#### Interview questions

**Q: How do you decide what to cache and where?**
A: Per data type, with a policy table: personalization (never shared-key), freshness demand (staleness bound), read/write ratio, refill cost. Layer by reach: browser/CDN for public, output cache for endpoint-shaped responses, service cache for shared domain data, L2 only when multiple processes must agree. Everything gets a TTL backstop and an invalidation trigger; the table is reviewed like a schema.

**Q: Explain the thundering herd and your standard defense stack.**
A: Synchronized expiry + concurrent misses → N simultaneous expensive refills at peak. Defenses in order: single-flight locking (one filler, waiters reuse — built into OutputCache/HybridCache), jittered TTLs (break alignment), early refresh (fill before expiry, off the hot path), and finally: accept it when refill is cheap. Measure the herd before choosing — E-16's demo numbers are the evidence pattern.

**Q: Why is "cache the cart page" usually wrong, and what is the right target?**
A: Because the page is per-user state — a shared cache key would replay one user's cart to others (a leak), and a per-user key multiplies entries by user count (memory) while helping only repeat views of one's own cart. The right targets are the *shared* inputs around it: product rows, prices-as-displayed, promo lists — cached at the service layer, invalidated by events. State belongs in the source of truth; caches belong around it.

#### Performance checklist

- [ ] Every cache in my system has: a layer, a TTL, an invalidation trigger, and a staleness bound — in a written policy table
- [ ] TTLs jittered; no synchronized expiry batches at deploy
- [ ] Stampede defense chosen per cache (single-flight where built-in; measured where accepted)
- [ ] No per-user data under shared keys; no money-accurate data from cache
- [ ] Cold-start behavior defined: empty cache = slower, not broken
- [ ] Danger checklist walked against production caches this quarter

---
### Chapter 20: HybridCache and the Distributed World

#### What this chapter is about

Chapter 18 made you hand-roll the hard parts — versioned keys, double-checked locking, size accounting. `HybridCache` (.NET 9, November 2024) ships all of them built-in in one API. This chapter rewrites ShopX's service cache with it, line by line, then explains the distributed tier (Redis) *conceptually* — what it is, when you need it, what it costs — with no infrastructure required. The chapter ends with the decision tree that keeps single-instance teams from cargo-culting Redis.

#### A short history of this topic

Before .NET 9, a correct cache was assembly work: `IMemoryCache` for the process + `IDistributedCache` (SQL Server/Redis implementations exist since 2.0) for cross-process, stitched by hand — and every hand-stitched version re-implemented the same three bugs: no stampede protection, no coherence between tiers, invalidation that only reached one tier. `HybridCache` (announced with .NET 9, GA November 2024) is the framework team's answer: one API, two tiers (L1 in-process, L2 `IDistributedCache` when present), single-flight built in, tags built in. It is the end of a decade of caching blog posts.

#### What it provides you

- The modern service-cache API: `GetOrCreateAsync` (a `ValueTask` — Chapter 13's payoff), entry options, tags, `RemoveByTagAsync`.
- A working mental model of L1/L2 coherence, and of Redis as a *concept* — enough to decide, design, and discuss without running it.

#### Why you should use it

Because the rewrite below replaces ~40 lines of careful machinery (E-16's gate, Ch 18's version dance) with ~4 lines that do *more* (stampede protection and tag eviction across both tiers). Correctness-by-default is a performance feature: your team stops shipping the bugs that become incidents.

##### 20.1 The rewrite

Install the package into `ShopX.Api` (NuGet: **`Microsoft.Extensions.Caching.Hybrid`**), register it, and replace `CategoryService`:

```csharp
using Microsoft.Extensions.Caching.Hybrid;
using ShopX.Data;
using ShopX.Data.Entities;

namespace ShopX.Api.Services;

public class CategoryService(HybridCache cache, ShopXContext db)
{
    public async ValueTask<IReadOnlyList<Category>> GetCategoriesAsync(CancellationToken ct)   // 1
    {
        return await cache.GetOrCreateAsync(
            "catalog:categories",                                    // 2 — stable key: no version dance
            async token =>                                           // 3
            {
                return await db.Categories.AsNoTracking()
                    .OrderBy(c => c.Name)
                    .ToListAsync(token);
            },
            new HybridCacheEntryOptions
            {
                Expiration           = TimeSpan.FromMinutes(30),      // 4 — shared (L2) lifetime
                LocalCacheExpiration = TimeSpan.FromMinutes(10),      // 5 — per-process (L1) lifetime
            },
            tags: ["catalog"],                                       // 6
            cancellationToken: ct);
    }

    public async Task InvalidateAsync(CancellationToken ct)
        => await cache.RemoveByTagAsync("catalog", ct);              // 7
}
```

Registration, one line: `builder.Services.AddHybridCache();`

Line by line — and note what is *gone* compared to Chapter 18:

- **1 — `async ValueTask<…>`**: on an L1 hit the call completes *synchronously* — Chapter 13 §13.3's fast path, zero allocation (a `Task`-returning API would allocate on every hit). This is what "API designed for hot paths" looks like.
- **2 — a stable key**: no version number embedded. Invalidation now happens by *tags* (6/7), not by making keys unreachable.
- **3 — the factory** receives its own `token` (HybridCache composes internal cancellation into your fill work); we pass `token` into EF.
- **4/5 — two clocks**: `Expiration` is the shared-tier lifetime; `LocalCacheExpiration` the per-process copy (keep it ≤ `Expiration` — a process should not outlive the shared truth). With no L2 registered (our lab today), you have an in-memory cache; set both now and the numbers are already right for the day Redis appears — same code, no redeploy required to *add* the tier.
- **6 — `tags: ["catalog"]`**: a C# 12 *collection expression* — compiler-generated code for "list of these". Both tiers record the tag; eviction (7) clears every `catalog`-tagged entry *everywhere the tag is known* — the cross-process invalidation Chapter 18's version numbers could not reach.
- **7 — `RemoveByTagAsync`**: the write path's one-liner (call it after `SaveChanges`, ordering rule from Chapter 10.3 intact: evict after commit).

Built-in, with no code from you: **single-flight per key** (E-16's `lock` mode — one filler, waiters reuse), coherence between tiers (an L2 update invalidates other processes' L1 entries for that key), and serialization only where needed (L1 stores *objects*, no serialization on hits; L2 pays bytes — Chapter 19.6's "serialization cost" row, solved by design).

The before/after ledger (put it in LAB.md E-17):

| Concern | Ch 18 hand-rolled | Ch 20 HybridCache |
|---|---|---|
| Stampede protection | ~15 lines, double-checked gate | built-in |
| Invalidation | version-number dance | `tags` + one call |
| Hit-path allocation | `Task` + entry alloc | `ValueTask`, zero on L1 hit |
| Cross-process coherence | impossible (per-process) | built-in when L2 present |
| Lines of code | ~40 | ~4 |

*(API shapes are as of the .NET 9/10 packages; if your package version differs slightly, Appendix C's docs page is the source of truth — one honest sentence, always.)*

##### 20.2 The distributed tier (Redis), without installing anything

**What Redis is:** an open-source, in-memory key-value server (single-threaded core, sub-millisecond ops). Data types beyond strings — hashes, sets, sorted sets — power counters, leaderboards, session stores. Eviction is configurable (`maxmemory` + `allkeys-lru` typically). Optional persistence (RDB snapshots / AOF log) survives restarts.

**Why it exists for us:** `IMemoryCache`/HybridCache-L1 is *per process*. ShopX runs two processes (Api on 5080, Web on 5180) — two L1s that do not see each other's invalidations (Chapter 18's honest note). In production you also run *multiple instances per app* behind a load balancer. The shared tier is what makes them agree:

```
        ┌────────► Api #1 ─┐                          ┌──── L1 (in-process, objects)
clients ─► load balancer ──┤                          │   ~0.05 ms, no serialization
        └────────► Api #2 ─┼──► Redis (shared tier) ──┤
        (and Web #1..n)    │    L2, ~0.5–2 ms,        └──── every miss refills from L2,
                             bytes (serialized)             not from SQL
                                    │
                                    ▼  only on L2 miss
                             SQL Server (the truth)
```

Read the flow: hit in L1 → never leaves the process (fastest, no serialization). Miss in L1 → L2 hit (~1 ms, bytes back, deserialized, *and* copied into L1 for next time). Miss in both → one request fills L2 (single-flight), others wait; SQL runs once.

**The one-line upgrade path** (when the day comes — no action needed today): register an `IDistributedCache` implementation and HybridCache *automatically* uses it as L2:

```csharp
builder.Services.AddStackExchangeRedisCache(o => o.Configuration = "localhost:6379");
// that's it — AddHybridCache() then wires Redis in as L2 for every entry.
// Output cache has the same upgrade: AddStackExchangeRedisOutputCache(...) (Ch 10's store, shared).
```

`AddStackExchangeRedisCache` — the `Microsoft.Extensions.Caching.StackExchangeRedis` package; the connection string points at the Redis server. Everything else — including the L1/L2 coherence — is HybridCache's job.

**What L2 costs** (the honest column): a network hop and serialization on every L2 hit (~1 ms and some CPU — cheap versus a database query, expensive versus L1); an operational surface (a server to run, monitor, patch, size — Chapter 29 applies); and a new failure mode (cache unavailable → must fall through to source, *not* error out — HybridCache handles the fallthrough; your sizing handles the stampede that follows a Redis restart, Chapter 19.5).

##### 20.3 The decision tree (when you actually need Redis)

```
How many instances of your app run concurrently?
├── 1 (single process) ──────────────► L1 only. Redis adds ops burden and zero benefit.
├── A few, behind one box, sticky-free, caching shared data
│        └── invalidation must reach all? ── no ──► L1 only + per-instance TTL (staleness bounded)
│                                     └── yes ──► L2 (shared tier): invalidation & coherence
└── Many / autoscaled / multiple regions ─► L2, plus CDN for public responses (Ch 9)
```

And the inverse rule: **removing** a tier is also an optimization — the smallest cache that meets the freshness bound wins (Chapter 19's table decides the bound; this tree decides the placement).

##### 20.4 Measuring the change

Same lab, L1-only, no L2 registered (deliberate: single-machine lab, honest per the tree above):

| Metric (illustrative) | Ch 18 | Ch 20 |
|---|---|---|
| `GET /api/categories` hit | ~0.3 ms | **~0.15 ms** (ValueTask path, zero alloc) |
| Stampede burst at expiry | 1 fill (with my 15-line gate) | **1 fill, no code** |
| Invalidation | version bump (this process only) | tags (L2-ready) |

LAB.md **E-17**: run the burst demo semantics against the new service (hit `/api/categories` under k6, watch the miss counter: one fill per expiry, waiters served). The engineering win — correctness by default — is the headline; write it in the mechanism column.

#### Measuring the change

Covered in 20.4. The strategic sentence for LAB.md: *the same policy table (Ch 19) now executes with less code and fewer failure modes; the tier decision (L1-only) follows the tree, not fashion.*

#### Key takeaways

- `HybridCache`: `GetOrCreateAsync(key, factory, options, tags)`; `ValueTask` hit path (zero-alloc, Ch 13 payoff); single-flight and tags built-in; `Expiration` (L2) vs `LocalCacheExpiration` (L1).
- Registering any `IDistributedCache` (e.g., `AddStackExchangeRedisCache`) upgrades the same code to two-tier — L2 is a placement decision, not a rewrite.
- L1 = objects in-process (fastest, no serialization); L2 = bytes shared across processes (~1 ms + serialization); source only on double miss, once (single-flight).
- Redis conceptually: in-memory KV server, eviction via maxmemory+lru, optional persistence; it is *an operations decision* — the decision tree, not FOMO, decides.
- Smallest tier that meets the freshness bound wins — deleting tiers is also optimization.

#### Exercises

**Exercise 20.1 — Coherence walkthrough.** With L2 present: Api #1 evicts tag `catalog` after an order. Trace what every other process serves over the next 30 seconds.

*Solution.* `RemoveByTagAsync` clears the L2 entries *and* the local L1 of the calling process. Other processes still hold L1 entries — until HybridCache's coherence mechanism expires/invalidates them: per its design, L1 entries carry a short `LocalCacheExpiration` and are cross-checked against L2 on access (implementation checks the L2's entry state on the next lookup, and L1 TTL bounds the window). Worst case: another process serves its up-to-10-minutes-old L1 copy — *that is why `LocalCacheExpiration` must be ≤ `Expiration`* — and that bound is the documented staleness budget (Ch 19's table), not an accident. The lesson: in two-tier caches, *per-process TTL is the coherence budget*.

**Exercise 20.2 — To L2 or not to L2.** Service A: single instance, 40 rps, refills 8 ms. Service B: 12 autoscaled instances, shared reference data, refills 300 ms, invalidation must be global within a minute. Decide and justify both.

*Solution.* A: L1-only — a second tier buys nothing at 40 rps with 8 ms refills (even a full stampede is ~a few concurrent 8 ms fills); adding Redis would add ops cost and a failure mode to save milliseconds that do not exist. B: L2 required — 12 instances × per-process caches cannot share invalidation; per-instance TTL-only would leave 12 divergent views for the whole TTL. With L2, one `RemoveByTagAsync` reaches all instances (bounded by their `LocalCacheExpiration`), and single-flight protects the 300 ms refill across the fleet. Note both answers cite the *tree*: instance count, refill cost, freshness bound.

#### Interview questions

**Q: What does HybridCache give you over IMemoryCache + IDistributedCache hand-wiring?**
A: Single-flight per key across the fill path (no stampede code), two-tier coherence (L1 invalidation coordinated with L2), tags for eviction (no version-key dances), `ValueTask` hot path (zero-alloc hits), and serialization only at the L2 boundary. The pre-.NET 9 wiring required all of that by hand, and most implementations got at least the stampede or the coherence wrong.

**Q: Why keep an L1 at all when Redis is fast?**
A: Because "fast for a network call" (sub-ms) and "no network call" (nanoseconds, no serialization) are different currencies — L1 hits serve *objects* with zero serialization; L2 hits pay a hop plus bytes both ways. On a hit-heavy read path that difference is the whole cost model (Ch 19.6). L1 also shields Redis from hot-key concentration and keeps working through L2 blips (with bounded staleness — that is the coherence trade you accept consciously).

**Q: Your cache is empty after every deploy and the site melts for two minutes. Diagnose and fix.**
A: Cold-start stampede: fresh process, empty tiers, first traffic wave fills everything at once (Ch 19.5/Ch 19's "must degrade to slower, not broken"). Fixes: single-flight (one fill per key), pre-warm the hot set at startup (hosted service fetching the top-N keys before taking traffic), gradual rollout (canary percentages — Ch 29), and rate limiting (Ch 22) as the circuit breaker. Also check that TTLs are jittered so the *second* expiry doesn't synchronize the herd.

#### Performance checklist

- [ ] `AddHybridCache()` in both apps; `CategoryService` rewritten (ValueTask, tags, dual expirations)
- [ ] The hand-rolled vs built-in ledger in LAB.md E-17 (my before/after)
- [ ] `LocalCacheExpiration ≤ Expiration` everywhere; the coherence budget is written down
- [ ] L2 decision made *by the tree* (single-instance lab = L1-only, honestly recorded)
- [ ] I can explain L1 vs L2 costs (objects vs bytes; 0.05 ms vs ~1 ms orders of magnitude)
- [ ] I can describe Redis, its eviction model, and its trade-offs without notes

---
## Part VI: Networking, I/O, and Resilience

Chapter 14 planted a sin on purpose: `new HttpClient()` per call. Part VI collects it. Chapter 21 shows the socket-exhaustion failure mode live, fixes it with typed clients + `IHttpClientFactory` + the standard resilience handler, and Chapter 22 protects ShopX from the world with the built-in rate-limiting middleware. Together they close the "external calls" row of the Chapter 7 budget.

### Chapter 21: HttpClient Done Right

#### What this chapter is about

`HttpClient` looks like any disposable object — `using var http = new HttpClient()` compiles, runs, and *works*. Under load it exhausts the machine's ports, because disposing a client does not dispose the TCP states the kernel still owes the network (TIME_WAIT). This chapter reproduces that failure on ShopX's reviews client, fixes it properly (typed client, pooled handlers, connection lifetime, resilience), and measures the before/after in the OS's own numbers.

#### A short history of this topic

`HttpClient` (2012, with async/await) had a trap baked in: its *underlying handler* holds real sockets, and handlers are expensive to create; the wrapper is cheap and misleadingly disposable. The community burned years on this. `IHttpClientFactory` (ASP.NET Core 2.1, May 2018) was the framework's formal answer: pool handlers, rotate them on a timer (DNS refresh), inject configured clients. `SocketsHttpHandler` (2.1) made the underlying pool first-class — and modern guidance (.NET 9+) is to control freshness through `PooledConnectionLifetime` rather than rotating whole handlers. The resilience story consolidated in .NET 8: `Microsoft.Extensions.Http.Resilience` wraps Polly-style pipelines (timeout, retry, circuit breaker) into one `AddStandardResilienceHandler()` — Chapter 14's era had to hand-assemble all of this; you get one line.

#### What it provides you

- The typed-client pattern (DI, configuration, logging) with the two knobs that matter: handler/connection lifetime and the standard resilience pipeline.
- The socket-exhaustion diagnosis: what TIME_WAIT is, how to count it, and why "it works on my machine" is exactly the wrong evidence.

#### Why you should use it

Because every real app is a client of something else (payments, search, reviews, email). The failure mode is *load-triggered* — invisible in dev, first appearing in production at peak: exactly the Chapter 12 pattern, one layer down.

##### 21.1 The crime scene

Current state (Chapter 14, ShopX.Web): every review call constructs and disposes an `HttpClient`. Reproduce the failure:

1. Start both apps (Ctrl+F5). Put 3–4 items in a cart (so the cart page fans out review calls).
2. Load the cart page hard:

```js
// cart-load.js
import http from 'k6/http';
export const options = { stages: [{ duration: '2m', target: 200 }], thresholds: { http_req_failed: ['rate<0.5'] } };
export default function () {
    http.get('http://localhost:5180/cart', { tags: { name: 'cart' } });
}
```

3. While it runs, count the machine's TIME_WAIT sockets (Windows):

```text
netstat -an | find /c "TIME_WAIT"
```

Illustrative on my box: within ~90 seconds the count climbs through **6,100+**, all between :5180's outbound ports and :5080; then the reviews calls start throwing `SocketException: Only one usage of each socket address...` and k6's failure rate ticks up. What happened, mechanically:

- Each `new HttpClient()` → new handler → new connection pool → **one new TCP connection per request** (nothing is reused — the pool lives one request).
- Disposing the client closes the socket, but the *kernel* keeps the tuple (source port → 5080) in **TIME_WAIT** for minutes by default (Windows: ~4 minutes unless tuned) — that is TCP being correct, not .NET leaking: TIME_WAIT exists so late packets from the old connection cannot confuse a new one using the same ports.
- Windows has ~16,000 dynamic source ports (49152–65535). Once they are *held in TIME_WAIT toward the same destination*, new connections to that destination fail: the machine is out of ports — for everyone, not just your app.

A 200-line chapter-14 review: the fix is not "dispose better" — it is **stop creating handlers per request**, which is what pooling means.

##### 21.2 The fix, in one registration

Rewrite `ReviewsClient` (Web) as a *typed client* — the class asks for an `HttpClient`; the factory supplies a good one:

```csharp
using System.Text.Json;

namespace ShopX.Web.Services;

public class ReviewsClient
{
    private readonly HttpClient _http;                       // 1
    public ReviewsClient(HttpClient http) => _http = http;

    public async Task<string?> GetLatestReviewAsync(int productId, CancellationToken ct)
    {
        var json = await _http.GetStringAsync($"api/mock/reviews/{productId}", ct);   // 2
        using var doc = JsonDocument.Parse(json);
        return doc.RootElement[0].GetProperty("text").GetString();
    }
}
```

- **1 — `HttpClient _http` injected, never disposed by us**: the factory owns the handler underneath; instances of the client class may come and go (transient is fine) — the *pool* persists.
- **2 — relative URL**: works because `BaseAddress` is set at registration; the pooled connection to `localhost:5080` is **reused across every call** — one connection (or a small set), zero port churn.

Registration (Web `Program.cs`; install package **`Microsoft.Extensions.Http.Resilience`**):

```csharp
builder.Services.AddHttpClient<ReviewsClient>(c =>
    {
        c.BaseAddress = new Uri("http://localhost:5080/");
    })
    .SetHandlerLifetime(TimeSpan.FromMinutes(5))              // 3
    .AddStandardResilienceHandler();                         // 4
```

Line by line:

- **3 — `.SetHandlerLifetime(TimeSpan.FromMinutes(5))`**: rotate the pooled handler (and thus its connections) at most every 5 minutes. Purpose: **DNS refresh** — a pooled connection living forever pins the app to one resolved address; rotation re-resolves. This is the classic knob; on .NET 9+ the same goal is achieved inside the default `SocketsHttpHandler` via `PooledConnectionLifetime` (the factory's modern defaults do this for you) — either knob is correct; pick one and know *why* it exists: bounded connection age, not handler hygiene.
- **4 — `.AddStandardResilienceHandler()`**: attaches the standard pipeline around every call this client makes:

| Stage | Default behavior | What it protects against |
|---|---|---|
| Total request timeout | e.g. ~30 s overall cap | runaway calls holding threads (Ch 12) |
| Retry | a few attempts, exponential backoff + jitter | transient blips (restarts, momentary 5xx) |
| Circuit breaker | opens after sustained failures | hammering a dying dependency (Ch 19's herd, at the *client* side) |
| Attempt timeout | per-attempt cap | hangs within an otherwise-successful window |

(Defaults are sensible; override per client as needed:)

```csharp
    .AddStandardResilienceHandler(o =>
    {
        o.TotalRequestTimeout.Timeout = TimeSpan.FromSeconds(10);
        o.Retry.MaxRetryAttempts = 2;                         // 5
        o.CircuitBreaker.FailureRatio = 0.5;
    });
```

- **5 — `o.Retry.MaxRetryAttempts = 2`**: note the interaction with Ch 14's fan-out: retries multiply calls — a 4-item `WhenAll` with 2 retries each is up to 12 requests in the worst case. And the safety rule: **only retry what is safe to repeat** — GETs yes; POSTs only with idempotency keys; never blind-retry a payment. The standard handler's defaults already respect HTTP semantics (it retries only what's retryable); when you tune it, keep that property.

##### 21.3 What the factory actually does (the mental model)

- One `HttpClient` wrapper per injection site (cheap, stateless) over a **pool of `HttpMessageHandler`s** (expensive, socket-owning), rotated per `SetHandlerLifetime`/connection lifetime.
- Every client gets *its* configuration (BaseAddress, headers, timeouts) while sharing the machinery — that is why typed clients beat a static `HttpClient` for multi-service apps (per-service config, per-service resilience, per-service logging), and why a lone static client is acceptable only for one simple destination.
- Built-in logging + `EventSource` counters (Chapter 29 will see them) — you get per-client request metrics for free.

##### 21.4 Measuring the fix

Rerun the cart load exactly as in 21.1 (same VUs, same duration). Illustrative before/after, from my machine:

| Metric | `new HttpClient()` per call | Typed client + pooling |
|---|---|---|
| TIME_WAIT toward :5080 | 6,100+ and climbing | **< 150, flat** |
| Socket exceptions | starts ~90 s in | **none** |
| Cart page p95 under 200 VUs | degrades (review calls fail → 500s) | **stable** (retried blips absorbed) |
| Connections to :5080 | one per request | **2–4 total** |

LAB.md **E-18**, with the mechanism line: *pooling replaces per-request connection creation; bounded lifetime keeps DNS fresh; resilience absorbs transients so the fan-out never amplifies into user-visible failures.*

#### Measuring the change

That is E-18. One honest caveat for the record: on localhost, connection setup is nearly free — the demo *fails anyway* because TIME_WAIT is a kernel-level resource exhaustion, not a latency cost. In production the same bug is worse: real handshakes (TCP+TLS) are tens of milliseconds *per call* you no longer pay, and TLS session reuse across a pooled connection is one of the biggest hidden wins of handler pooling.

#### Key takeaways

- Never `new HttpClient()` per call; the handler holds sockets; dispose ≠ kernel-level freedom (TIME_WAIT lasts minutes; ~16k dynamic ports per machine).
- Typed clients: `AddHttpClient<T>(c => …).SetHandlerLifetime(…).AddStandardResilienceHandler()`; factory pools handlers, rotates for DNS, injects config + logging.
- Modern .NET: `SocketsHttpHandler.PooledConnectionLifetime` is the connection-freshness knob; one mechanism, know why it exists.
- Retries interact with fan-out (attempts multiply); retry only idempotent operations; circuit breakers protect the *downstream* from you.
- Diagnose with the OS: `netstat` TIME_WAIT counts; load-triggered port exhaustion is a production-only surprise by design.

#### Exercises

**Exercise 21.1 — Pool sizing arithmetic.** The reviews service accepts 200 connections comfortably. Web runs 4 instances behind a load balancer, each with `MaxConnectionsPerServer`-style defaults. When does the pool matter, and what happens at a flash crowd of 5,000 concurrent review calls?

*Solution.* Pooled clients keep *few* long-lived connections per (instance × destination) — throughput comes from multiplexing requests over them (HTTP/1.1: one request at a time per connection — so concurrency = connection count; HTTP/2: many streams per connection). At 5,000 concurrent calls, pools grow toward each instance's limit; beyond it, calls queue at the client (waiting, not failing) — that is backpressure, which is what you want versus a socket storm. The flash crowd then spends its energy in queues and circuit breakers, not in kernel port tables. Set the limit *with the downstream owner* (Ch 14's bounded fan-out, now at the socket layer).

**Exercise 21.2 — Retry safety.** `AddItem` (cart POST) is wrapped in a standard resilience handler with retries enabled by copy-paste from a GET client. Give the failure sequence.

*Solution.* POST is not idempotent: attempt 1 times out client-side but *succeeds server-side*; retry inserts the item twice (or double-charges in a payment path). Sequence: timeout at 10 s → retry → duplicate write. Fixes: retry only GETs by default; for POSTs use idempotency keys (the server dedupes by key) or idempotency-aware retries (retry only on connection-level failures where the request provably never landed). The standard handler's GET-biased defaults exist precisely for this — the bug is overriding them without thinking.

#### Interview questions

**Q: Why is `new HttpClient()` per request an anti-pattern?**
A: Each instance owns a handler with its own connection pool — so every call opens a new TCP (and TLS) connection and, on dispose, leaves the kernel holding TIME_WAIT states for minutes. Under load this exhausts dynamic ports toward the destination and throws `SocketException`. Fix: one pooled client per destination via `IHttpClientFactory` (typed clients), handler/connection lifetime bounded for DNS freshness.

**Q: `SetHandlerLifetime` vs `PooledConnectionLifetime` — what's the difference and which do you use?**
A: Handler lifetime rotates the whole `HttpMessageHandler` pool entry every N minutes (older mechanism; also rotates everything the handler carries, e.g. cookie state). `PooledConnectionLifetime` (SocketsHttpHandler) bounds each *connection's* age while the handler lives on — finer-grained, cheaper, and the .NET 9+ guidance. Either achieves DNS refresh; don't set both to tiny values (you re-handshake constantly and lose TLS session reuse).

**Q: What does the standard resilience handler do, and when do you NOT want retries?**
A: Pipeline: total timeout → retry (backoff+jitter) → circuit breaker → per-attempt timeout, with HTTP-aware defaults. No retries when the operation is non-idempotent without an idempotency key (POSTs, payments), when the downstream is saturated (retrying into a full system amplifies the outage — the circuit breaker is there to stop you), or when the caller already has its own timeout shorter than the retry chain (you'll abandon work server-side, wasting capacity — Ch 13.5's cancellation lesson, one layer up).

#### Performance checklist

- [ ] No `new HttpClient()` in ShopX (or anywhere I own) — searched: `new HttpClient`
- [ ] `ReviewsClient` is a typed client with BaseAddress, `SetHandlerLifetime`/connection lifetime, standard resilience
- [ ] TIME_WAIT before/after measured in LAB.md E-18 with netstat counts
- [ ] Retry policy reviewed for idempotency; circuit breaker present on external calls
- [ ] Per-destination pool limits agreed with the owning team (bounded fan-out at the socket layer)
- [ ] I can explain TIME_WAIT and the ~16k port budget without notes

---
### Chapter 22: Rate Limiting to Protect the App

#### What this chapter is about

Everything so far makes ShopX fast; rate limiting makes it *survive*. This chapter covers the built-in middleware (.NET 7+): the four algorithms (fixed window, sliding window, token bucket, concurrency), per-IP global limits, per-user policies on the write paths, the correct `429` + `Retry-After` behavior, and the measurement discipline that keeps your own load tests and monitors from being throttled.

#### A short history of this topic

Before .NET 7 (November 2022), ASP.NET Core had no in-box rate limiting — teams wired proxies (nginx `limit_req`), cloud WAFs, or the `System.Threading.RateLimiting` primitives by hand. .NET 7 moved the algorithms into the runtime (`System.Threading.RateLimiting`) and exposed them as middleware with partitioning — per user, per IP, per API key — which is the part that makes it *usable*: a global counter that throttles your best customer because a scraper is hungry is not a policy.

#### What it provides you

- The four algorithms and their trade-offs (which one for which job).
- Working configuration: global per-IP + per-user on writes, correct rejection semantics, k6 proof that p95 for *allowed* users survives a spike.

#### Why you should use it

Because caching (Part V) and pooling (Ch 21) make each request cheap, but *capacity is still finite*. Without admission control, one hostile client (or one enthusiastic integration partner, or your own retry storm — Ch 21.2's interaction) converts "cheap" into "down". Rate limiting is the circuit breaker between the world and your thread pool.

##### 22.1 The four algorithms

| Algorithm | Grants | Allows bursts | Weakness | Use for |
|---|---|---|---|---|
| **Fixed window** | N per window (e.g. 100/min) | up to 2×N at window edges | edge bursts: 100 at 11:59:59 + 100 at 12:00:00 | coarse global caps |
| **Sliding window** | N per rolling window | yes, smoothly | more state | smoother versions of fixed |
| **Token bucket** | bucket of B tokens, refilled R/period | yes (up to B), then steady R | tuning B vs R | APIs: allow a burst, then a rate — the standard choice |
| **Concurrency** | N in flight at once | N concurrent | protects *capacity*, not rate | the one that directly shields the thread pool (Ch 12!) |

The deep point: **concurrency limiting protects your resources; rate limiting protects your quotas.** Under an incident, the concurrency limiter is the one that keeps queue lengths near zero — it is Chapter 12's lesson turned into middleware.

##### 22.2 ShopX configuration (both apps)

Web `Program.cs` (mirror in Api with the same shape):

```csharp
builder.Services.AddRateLimiter(o =>
{
    o.RejectionStatusCode = StatusCodes.Status429TooManyRequests;          // 1

    o.OnRejected = async (ctx, ct) =>                                      // 2
    {
        ctx.HttpContext.Response.Headers.RetryAfter = "10";
        await ValueTask.CompletedTask;
    };

    o.AddPolicy("per-user", ctx =>                                        // 3
        RateLimitPartition.GetTokenBucketLimiter(
            ctx.Connection.RemoteIpAddress?.ToString() ?? "anon",          // 4
            _ => new TokenBucketRateLimiterOptions
            {
                TokenLimit = 10,                                          // 5
                TokensPerPeriod = 10,
                ReplenishmentPeriod = TimeSpan.FromSeconds(30),
                QueueLimit = 0,                                           // 6
            }));

    o.GlobalLimiter = PartitionedRateLimiter.Create<HttpContext, string>(ctx =>   // 7
        RateLimitPartition.GetFixedWindowLimiter(
            ctx.Connection.RemoteIpAddress?.ToString() ?? "unknown",
            _ => new FixedWindowRateLimiterOptions
            {
                PermitLimit = 200,
                Window = TimeSpan.FromMinutes(1),
                QueueLimit = 0,
            }));
});
```

and in the pipeline, per the Chapter 8 chart — after `UseRouting`, **before** the caches:

```csharp
app.UseRateLimiter();
```

Line by line:

- **1 — `RejectionStatusCode = 429`**: the correct semantic (Too Many Requests), not 403/500 — clients and retries (Ch 21) behave differently for 429.
- **2 — `OnRejected`**: writes `Retry-After: 10` (seconds) so well-behaved clients back off instead of immediately retrying into the same wall. `ValueTask.CompletedTask` — the no-alloc completed-task singleton (Ch 8's trick).
- **3 — `AddPolicy("per-user", ctx => …)`**: a named, *partitioned* policy: the lambda receives the `HttpContext` and returns a `RateLimitPartition` — **the partition key decides who shares a bucket**.
- **4 — the partition key**: `RemoteIpAddress` (our anonymous shoppers have no login; in a real app with auth, partition on `User.Identity.Name` or the API key — *never on nothing*, which would make all users share one bucket).
- **5 — token bucket, 10 tokens / 30 s**: a burst of 10 cart writes, then a sustained ~20/min — generous for a human, hopeless for a script. `TokenLimit = TokensPerPeriod` = "burst equals one period's budget".
- **6 — `QueueLimit = 0`**: rejected immediately rather than queued — for write paths, queuing multiplies harm (Ch 12: waiting costs capacity); for read paths a small queue smooths spikes.
- **7 — `GlobalLimiter`**: per-IP fixed window, 200/min — the coarse outer gate every request passes, *then* endpoint policies apply (they compose: both must pass).

Apply the named policy where abuse hurts most — the cart write (both apps):

```csharp
[EnableRateLimiting("per-user")]
[HttpPost("items/{productId:int}/{quantity:int}")]
public async Task<IActionResult> AddItem(int productId, int quantity) { … }
```

`[EnableRateLimiting]` reads the endpoint metadata — that is why `UseRateLimiter` must come after `UseRouting` (Ch 8's rule, now mechanical). Unauthenticated-but-expensive endpoints can use `[DisableRateLimiting]` to opt *out* of the global limiter (health checks — Chapter 29 — must never be throttled: a throttled health probe is how you autoscale into an outage).

##### 22.3 Proving it: the spike test

```js
// spike.js — burst against the cart write
import http from 'k6/http';
import { check } from 'k6';
export const options = {
  scenarios: { spike: { executor: 'ramping-arrival-rate', startRate: 1, timeUnit: '1s',
                        stages: [{ target: 100, duration: '20s' }], preAllocatedVUs: 20 } },
};
export default function () {
    const r = http.post('http://localhost:5180/cart/add/7');
    check(r, {
        'throttled (expected under spike)': r.status === 429,
        'accepted': r.status === 200 || r.status === 302,
    });
}
```

Illustrative result on my box: first 10 posts in a burst land (200/302), then a stream of **429s with `Retry-After: 10`**, and — the number that matters — the *other* endpoints' p95 during the spike stay flat (the write path cannot drag the read path down, because it was never allowed to accumulate). **LAB.md E-19**: paste the status-code distribution and the read-path p95 during the spike.

Two measurement disciplines:

1. **Report 429s separately.** k6's default `http_req_failed` counts 429 as failure — under a protection test that inverts the story ("the site is failing" vs "the shield is working"). Tag the spike traffic (`{ tags: { name: 'spike' } }`), and read its status distribution as a *check*, not an error.
2. **Don't throttle your own instruments.** Load tests, uptime monitors, and health probes all look like attackers to a rate limiter. Options: partition keys that exclude known internal ranges, higher limits for authenticated service accounts, or `Allow` lists per policy. Decide explicitly and write it down — the alternative is discovering your monitor was silently 429ing for a month.

##### 22.4 The honest limits of in-process limiting

This limiter is per-process. Ten instances each allow 200/min/IP → your real global limit is 2,000/min/IP. That is usually fine (it bounds *work per instance*, which is what protects you), but if you need a *global* policy (public API quotas you sell), the counter must live somewhere shared — the same L2 tier as Chapter 20 (a distributed counter in Redis, or at the edge: CDN/WAF per-second limits). Know which product requirement you have; in-process protects capacity, edge protects quota.

#### Measuring the change

E-19 as above. The scoreboard row that matters is not rps — it is **p95 of allowed traffic during an attack-shaped burst**, which stays flat instead of melting. That is what "the app is protected" means, measurably.

#### Key takeaways

- Four algorithms; **concurrency protects capacity, rates protect quotas**; token bucket is the default API choice (burst + steady rate).
- Partition keys decide who shares a bucket — per user/IP/key, never "everyone together"; policies compose with the global limiter (all must pass).
- Semantics: `429` + `Retry-After`; `QueueLimit = 0` for writes (do not queue abuse); `UseRateLimiter` after `UseRouting`, before caches; health checks never throttled.
- Measure: 429s reported separately from failures; allowed-traffic p95 during spikes is the metric of success.
- In-process limiting bounds per-instance work; global quotas need a shared tier (edge or L2).

#### Exercises

**Exercise 22.1 — Choose the algorithm.** For each: (a) a payment-status polling endpoint; (b) a search API sold with "100 requests/min" SLA; (c) a report generator that pins CPU.

*Solution.* (a) Concurrency limiter — the endpoint is cheap but chatty; what you cannot afford is unbounded in-flight polls during an incident; a rate would also work, but concurrency maps directly to the resource at risk. (b) Token bucket per API key (10–20 burst, 100/min refill — read the SLA as the refill rate; the bucket size is a *product* decision, record it). (c) Rate limit (low N) *and* a queue with a timeout for the user experience — but the real fix is moving reports off the request path (a queue + hosted worker, Ch 29); the limiter is the seatbelt while the architecture gets fixed.

**Exercise 22.2 — The retry storm.** A client integrates your API with retries on 429 *without* honoring `Retry-After`. Describe the equilibrium you converge to, and two server-side mitigations.

*Solution.* Client gets 429 → retries immediately → hits the same full bucket → more 429 → its retry loop saturates itself doing nothing (good: it self-throttles), but it still burns your limiter's CPU and log volume, and if its retry count is high, it eventually lands requests *between* other clients' tokens — the noisy-neighbor stays noisy at reduced efficiency. Mitigations: honor `Retry-After` yourself (server can raise the value under stress to push backoff out), and cap *per-connection* concurrency (Ch 21 pool limits) so a single client cannot occupy all in-flight slots; extreme cases → edge blocks (WAF rules on 429 rates) — you cannot fix clients, but you can make their misbehavior cheap for you.

#### Interview questions

**Q: Fixed window vs token bucket — one sentence each, and when the difference matters.**
A: Fixed window: N per calendar window (edge bursts up to 2N); token bucket: capacity B, refill R/period (burst B, then sustained R). The difference matters at window edges (marketing emails sent at 12:00) and whenever the product promise is "burst then steady" — token buckets model that; fixed windows model "per minute" literally.

**Q: Why queue rejections for reads but not for writes?**
A: A queued read waits cheaply (no threads held, Ch 12) and converts a spike into a smooth line — good UX. A queued write extends the time the limiter holds state for an abuser, delays legitimate traffic behind them, and multiplies server work under attack (each queued item is still work later). For writes, fail fast with `Retry-After`; for reads, a small queue (a few seconds) buys smoothness.

**Q: What breaks if you place `UseRateLimiter` after the caching middleware?**
A: Two bugs: (1) rate decisions happen after cache lookups, so an attacker can still enjoy cache hits (fine) but your limiter never sees… actually the sharper bug: (2) a 429 can be *cached* by the response/output cache — the next N users of that key get a throttling response they did not earn, and the real attacker got the cache slot. Order: routing → rate limit → caches (Ch 8's chart; now you can derive it, not memorize it).

#### Performance checklist

- [ ] Both apps: `AddRateLimiter` with global per-IP + named per-user policy on cart writes
- [ ] `429` + `Retry-After` verified with curl; `[EnableRateLimiting]`/`[DisableRateLimiting]` placed deliberately
- [ ] `UseRateLimiter` after `UseRouting`, before the caches (derivable from first principles)
- [ ] Spike test run; allowed-traffic p95 during spike flat — in LAB.md E-19
- [ ] Monitors/load tests exempted *by decision*, written down
- [ ] I know the per-process vs global-quota distinction and where each is decided

---
## Part VII: Memory and the Runtime

Chapter 6's diagnosis left memory as the last unpaid bill: ~215 KB allocated per request at baseline (Chapter 5's arithmetic), still ~96 KB after Part IV. Part VII pays it down: how the GC actually works (Chapter 23), the allocation-aware coding toolkit (Chapter 24), and the runtime knobs — GC modes, JIT flags, publish options (Chapter 25). The order is deliberate: you cannot tune the collector (25) before you know what it does (23), and you should not micro-optimize allocations (24) where the profiler says structure already won.

### Chapter 23: Memory and the Garbage Collector

#### What this chapter is about

Every object your code creates is a future bill the GC must collect. This chapter explains the collector's model (generations, LOH, triggers, pauses), the one metric that predicts GC pain (**allocation rate**), the counters that expose it live, and the heap-census tools (`dotnet-gcdump`, `dotnet-dump`) that answer "what is holding my memory?". No ShopX changes — this is the theory Chapter 24 spends and Chapter 25 tunes.

#### A short history of this topic

.NET shipped with a generational, compacting GC in 2002 — the bet that *most objects die young*, so collecting the newest allocations is cheap and frequent. The bet won: Gen0 collections are sub-millisecond pauses. The costs that remained were the ones this chapter teaches you to see: the LOH (never compacted by default — fragmentation), concurrent/background collection (since 2.0, .NET 8's DATAS), and above all **allocation rate** — the GC's workload is proportional to what you allocate, not to how long your program runs. .NET's GC is one of the best ever shipped; the reason apps still suffer is almost never "the GC is slow" — it is "the app allocates like it is free".

#### What it provides you

- The cost model: `new` (reference type) = future GC work; boxing and closures allocate silently; large objects go straight to the expensive heap.
- Live counters: **Alloc Rate** (the leading indicator), Gen0/Gen2 counts, heap sizes.
- The two-dump diff method for "memory grows" tickets.

#### Why you should use it

Because tail latency (p99) is where GC pauses land, and p99 is where users and SLAs live (Chapter 3). Teams chase p99 with more servers; the cheaper fix is usually right here: fewer allocations → fewer collections → shorter rarer pauses.

##### 23.1 What allocates (the invisible price list)

| Construct | Allocates | Notes |
|---|---|---|
| `new` on a class | the object (+16 B overhead-ish) | every time |
| closure capturing a variable | a display class + the delegate | per creation site execution |
| `params` array (classic) | an array per call | `params ReadOnlySpan<T>` (C# 11+) avoids it — Ch 24 |
| boxing (value type → `object`/non-generic interface) | a heap copy | `ViewData["…"] = 5` boxes — Ch 26 fixes ours |
| LINQ chain | 1–2 objects per operator + the closure | Ch 24.3 |
| string ops (`+`, `Substring`, `Replace`, interpolation) | new strings | strings are immutable — every "change" is a new one |
| `Task` per async call | one Task (unless fast-pathed — Ch 13) | `ValueTask` on hot paths |
| arrays ≥ 85,000 bytes | **straight to the LOH** | see 23.2 — the expensive heap |
| async state machine (first suspension) | one box per suspension | §13.1 — cheap; the fast path avoids it |

Structs (including your DTOs-as-records? no — records are classes unless `record struct`) live where they are declared: locals on the *stack* (no GC, ever), fields inside their parent. The stack is free; the heap is billed.

##### 23.2 The collector's model

- **Generations 0/1/2**: new objects start in Gen0 (collected often, cheaply — most die there). Survivors are promoted: Gen1 (a buffer zone), Gen2 (collected rarely; a full Gen2 is the expensive one, with the longest pauses — though *background* GC sweeps most of Gen2 concurrently).
- **Budgets, not schedules**: collections trigger when a generation's *budget* of allocations is exhausted — which is why **allocation rate is the dial you actually control**: halve allocations and you halve collection frequency, whatever the mode.
- **LOH** (Large Object Heap): any single allocation ≥ 85,000 bytes goes there directly. Never compacted by default (compaction moves objects — expensive at that size), so LOH *fragments*: a 500 MB LOH can have 200 MB free in unusable gaps. A `List<Product>` of 20,000 tracked entities materialized at once — Chapter 4's original sin shape — is an LOH citizen.
- **POH** (.NET 5): the Pinned Object Heap — for buffers that must stay put (interop); you will know when you need it.
- **Pause times**: Gen0 ≈ sub-millisecond; Gen2 full pauses can reach tens of ms+ (background GC shortens them; the worst case is a *blocking* Gen2 during memory pressure). One per few seconds is invisible; one per request is p99 ruin.

##### 23.3 The metric: allocation rate

During a load run, watch (Ch 5's command):

```text
dotnet-counters monitor -n ShopX.Api --counters System.Runtime
```

- **Alloc Rate** (B/s): the leading indicator. Divide by rps (Ch 5's arithmetic) → KB/request.
- **Gen 0 GC Count** (per second): how often the cheap collection runs — fine at low single digits, watch it climb.
- **Gen 2 GC Count**: should be ~0. **Sustained Gen2s are the red alarm** — something big is surviving (caches in the wrong heap, LOH churn, roots holding graphs).
- **GC Heap Size / LOH Size**: the stock levels. Growth *between* load runs that never returns = leak-shaped (23.4).

ShopX's current state (illustrative, during the Ch 5-class load, post-Part IV): Alloc Rate ~3 MB/s at ~33 rps ≈ 96 KB/request, Gen0 ~0.4/s, Gen2 0, heap 70–90 MB, LOH flat. That is a healthy app — record yours in **LAB.md E-20**; Chapter 30's audit compares.

##### 23.4 "Memory grows": the two-dump diff

The method for "the box's RAM climbs until Thursday's restart":

```text
dotnet-gcdump collect -n ShopX.Api          # snapshot 1 (morning)
… run the load / let hours pass …
dotnet-gcdump collect -n ShopX.Api          # snapshot 2
dotnet-gcdump report Snap_1.gcdump
dotnet-gcdump report Snap_2.gcdump
```

`report` lists types by count and size. **Diff the two reports by type**: whatever grew between snapshots is your leak — 90% of the time it is one of: an unbounded cache (no `SizeLimit` — Ch 18's warning), event handlers never detached, static collections, or captured contexts (`closure` classes holding pages of state — the report literally shows `<>c__DisplayClass` names). `dotnet-dump` goes deeper (full process snapshot + `clrstack`, `dumpheap -stat`, `gcroot <address>` — who is holding whom), for post-mortems; gcdump's census is enough for most "grows" tickets.

##### 23.5 What ShopX holds today (the audit habit)

Walk the heap by *design*, not just by tooling: OutputCache store ~1 MB before Ch 27 slims the pages (Ch 10's note), HybridCache/IMemoryCache entries ~KBs, EF's per-request contexts (die with the request — Gen0 citizens, the *good* kind: die young), pooled connections (unmanaged, not GC-visible). The lesson to keep: **every cache you add is a heap resident with a TTL — its budget belongs in the same table as its staleness bound** (Ch 19's policy table gets a memory column in your head).

#### Measuring the change

E-20 as in 23.3 — this chapter's product is a *number you now track* (alloc/request and Gen2 ≈ 0), and a method you can run on any "memory grows" ticket without guessing.

#### Key takeaways

- Allocation = future GC work; allocation *rate* is the dial you control (halve it, halve collection frequency).
- Gen0 cheap/frequent (sub-ms), Gen2 rare/expensive; **sustained Gen2s are the alarm**; LOH (≥85,000 B) never compacts by default — avoid big transient buffers.
- Stack is free (structs, locals); heap is billed (classes, closures, boxing, LINQ iterators, strings, `Task`s).
- Two-dump gcdump diff answers "what is holding memory"; the usual suspects are unbounded caches and captured contexts.
- Healthy targets: alloc/request falling chapter by chapter (215 → 96 → …), Gen2 ≈ 0, flat LOH.

#### Exercises

**Exercise 23.1 — Classify the allocations.** For one `GET /api/products` request (post-Ch 16): list the allocation sources in order of size and say which generation each dies in.

*Solution.* Biggest first (illustrative shares of ~96 KB): DTOs + response objects (die Gen0 — same request; *good*), EF's command/reader machinery (Gen0), JSON source-gen buffers (Gen0/POH-ish pooled — small), Kestrel response buffers (pooled internally, not GC), the `X-Elapsed` middleware (zero — Ch 8), rate limiter state (pooled lease — no alloc on the happy path). Nothing survives the request → everything is Gen0-dying: the *desired* shape. If a Gen2 counter appears in this profile, something is surviving — that is the finding to chase.

**Exercise 23.2 — The Thursday restart.** Heap grows 40 MB/day, resets on restart, Gen2 3×/day and climbing by Friday. No code changes in a month. Walk your investigation.

*Solution.* Load steady → it is *state accumulation*, not traffic: take Monday + Wednesday gcdumps, diff by type (23.4). Hypotheses ranked by base rates: unbounded in-process cache (check `SizeLimit`/TTLs vs Ch 19 table), OutputCache entries with long TTLs accumulating varied keys (check key cardinality — `page` × `category` × `sort` multiplies variants; that is why vary sets are reviewed like schemas), log/history buffers, event subscriptions. The Gen2 climb says survivors are being promoted — the diff names the type; then decide: bound it (SizeLimit), shorten TTL, or accept and size memory (Ch 25's budgeting). The restart "fixing" it is the signature of accumulation, not fragmentation.

#### Interview questions

**Q: Why is "the GC is slow" usually wrong?**
A: The GC's cost scales with the app's allocation rate and survivorship, not with time. Pauses people notice (p99 spikes) are almost always full Gen2s provoked by heavy allocation or large-object churn. The fix is allocating less (projections, pooling, no boxing, source-gen), or the right GC mode (Ch 25) — not switching runtimes and inheriting the same physics.

**Q: What lands on the LOH and why does it hurt?**
A: Single allocations ≥ 85,000 bytes — big arrays, big lists materialized at once, large strings. It hurts twice: LOH collections are rarer and more expensive, and the LOH is not compacted by default, so freed blocks fragment instead of returning (an OOM with plenty of "free" memory). Fix at the source: stream instead of buffering (Ch 16.8), chunk instead of `ToList`-everything, pool big buffers (Ch 24.6).

**Q: Your p99 spikes correlate with Gen2 counts. Give the reduction plan in order.**
A: (1) Measure alloc/request and find the top allocating paths (counters + profiler allocation view); (2) structural first: projections/caching delete work outright (Part II/IV/V — biggest wins, already proven in this book); (3) allocation hygiene on the remaining hot paths (Ch 24: closures, boxing, LINQ, string churn, per-call Tasks); (4) runtime mode: Server GC / DATAS for throughput-vs-pause trade (Ch 25); (5) only then micro-tuning — and re-measure after every single step (Ch 1's rules, unchanged).

#### Performance checklist

- [ ] I can list what allocates from memory (closures, boxing, LINQ, strings, params, LOH)
- [ ] Alloc/request tracked in LAB.md across chapters (215 → 96 → …)
- [ ] Counters watched under load: Gen2 ≈ 0, flat LOH, Gen0 low single digits/s
- [ ] Two-dump gcdump diff run once on purpose (practice before the incident)
- [ ] Every cache has a memory budget next to its staleness bound
- [ ] I can explain generations/budgets/LOH without notes

---
### Chapter 24: Writing Allocation-Aware Code

#### What this chapter is about

This is the technique library for Chapter 23's bill: strings, closures, LINQ, boxing, `Span<T>`, `ArrayPool`, structs — each pattern with code, each cost with a BenchmarkDotNet number, each with the rule for *when* to apply it. The chapter ends differently from every other one: **we deliberately change nothing in ShopX**, and that decision is itself the lesson.

#### A short history of this topic

The primitives arrived in a wave across .NET's modern era: `Span<T>`/`Memory<T>` and `ArrayPool` (2.1, 2018) gave safe zero-copy views and buffer reuse; C# 7–8 (`in`, `readonly struct`, `stackalloc` in more contexts) made struct-passing cheap; source generators (Roslyn, .NET 5–6 era) moved reflection to compile time; C# 10–12 (`record struct`, `params ReadOnlySpan<T>`, collection expressions) kept tightening. Each exists because the allocation price list (Ch 23.1) was measured, somewhere, by someone — usually in the runtime team's own services.

#### What it provides you

- The seven patterns with measured costs — the vocabulary of allocation reviews.
- The decision rule that keeps you from cargo-culting them (and the Knuth moment at the end).

#### Why you should use it

Because p99 is GC-shaped (Ch 23), and these patterns are the only levers left once structure (caching, projections, streaming) has taken its share. Applied *where the profiler points*, they are cheap; applied everywhere, they wreck readability for nanoseconds nobody measured — the exact "small efficiencies" Knuth warned about, and this book refuses.

##### 24.1 The discipline

1. The profiler names the hot path (Ch 6); BenchmarkDotNet prices the fix (Ch 5) — *per candidate call site*, never in the abstract.
2. One change, re-measure (Ch 1). Allocation improvements show as **Allocated** column drops and, one step later, Gen0 count drops.

##### 24.2 Strings

```csharp
// ❌ O(n²): each += copies everything so far (Ch 5 measured: 24.6 KB, 21 µs @ N=200)
var s = "";  for (...) s += part;

// ✅ buffer, reused across iterations (Ch 5: 4.4 KB, 2.1 µs)
var sb = new StringBuilder(capacity: 512);  for (...) sb.Append(part);

// ✅✅ buffer, reused across CALLS — a pooled builder per hot service (0.2 KB steady, 1.7 µs)
private readonly StringBuilder _sb = new(512);
public string Build() { _sb.Clear(); …; return _sb.ToString(); }   // ToString still allocates the result — unavoidable: the *result* is the product
```

Rules: size the capacity (growth = copies + LOH risk for giant buffers); interpolation `$"{a} {b}"` compiles to `string.Concat`/`Append` — fine once per response, a problem per row; `string.Create` builds a string of known length in place (one allocation, no intermediate buffer); `TryFormat` writes numbers into a `Span<char>` you own.

##### 24.3 Closures and delegates

```csharp
// ❌ captures `threshold` → compiler emits a display class + allocates it per call
var hot = items.Where(x => x.Score > threshold);

// ✅ nothing captured → static lambda, zero allocation
var hot = items.Where(static x => x.Score > 90);
```

Cost (illustrative, from my machine): the capturing version allocates a closure object + delegate per evaluation (~64 B); the `static` one allocates nothing (the delegate is cached by the compiler). Same trap in `foreach` bodies that capture the loop variable into a lambda. If the value must come from a variable, hoist the *filter* into the call: `items.Where(MakePredicate(threshold))` still allocates once — fine *outside* per-row paths; the rule is about per-row/per-call sites.

##### 24.4 LINQ: know the toll, then decide

```csharp
// toll (illustrative, int[1000], my machine):
//   Where+Select+Sum via LINQ   1.9 µs   168 B   (three iterator objects)
//   the same with foreach      0.32 µs    0 B
```

LINQ's cost is small *absolute* and paid *per call site execution* — irrelevant in cold paths (run it, ship it, stay readable), real in per-row and per-request hot paths. Decision rule from the profiler, not from fashion: if a chain sits inside something called thousands of times per second, flatten it to a loop; otherwise keep the LINQ. (Note the overlap with EF: LINQ *outside* `IQueryable` is LINQ-to-Objects — Chapter 16's Exercise 16.1 was this chapter in disguise.)

##### 24.5 Boxing: the silent one

```csharp
ViewData["TotalPages"] = totalPages;      // int → object: a heap allocation + later cast-unbox
ViewBag.Total = 19.99m;                  // decimal boxed into a dynamic dictionary
if (someStruct is IDisposable) …          // interface test on a struct can box (use pattern + generics)
```

Boxing appears wherever value types meet `object`/dynamic/non-generic slots: `ViewData`/`ViewBag` (MVC — Chapter 26 removes ours), `ArrayList`-era APIs, logging with `object[]` args, `params object[]`. Each box is a small heap object that dies soon — cheap *once*, a steady Gen0 drip at scale. The fix is typed slots (view models, generic overloads — `Logger.LogInformation("{N}", value)` uses generics, no boxing).

##### 24.6 `Span<T>`: views instead of copies

```csharp
// Parse "SKU-00042" without allocating a substring (called per row in hot paths)
public static bool TryGetSkuNumber(ReadOnlySpan<char> text, out int number)
{
    if (text.Length < 8 || !text.StartsWith("SKU-"))            // span StartsWith: no allocation
    { number = 0; return false; }

    return int.TryParse(text.Slice(4), out number);             // span TryParse: no allocation
}
```

`Span<T>` is a *view* over existing memory (string chars, array, stackalloc) — slicing and parsing allocate nothing. Arithmetic: substring-per-row over 20,000 rows ≈ 20,000 × ~40 B = **800 KB per pass**; the span version allocates 0. Companions: `stackalloc` (small scratch buffers on the stack), `SearchValues<char>` (.NET 8: precomputed multi-char search — `span.IndexOfAny(s_separators)`), `string.Create`, `TryFormat`. `Span` rules: it is stack-only (cannot be a field — use `Memory<T>` for fields/async), single-threaded by contract.

##### 24.7 `ArrayPool<T>`: rent, return, repeat

```csharp
public byte[] Handle(Request r)
{
    var buffer = ArrayPool<byte>.Shared.Rent(64 * 1024);         // 1 — likely zero allocation
    try
    {
        /* fill, transform, hand off — e.g., a per-request scratch buffer */
        return Compress(buffer);
    }
    finally { ArrayPool<byte>.Shared.Return(buffer); }           // 2 — always
}
```

- **1 — `Rent`**: returns a buffer *at least* that size from a shared pool (per-CPU buckets) — steady state: zero `new byte[]` (and no LOH citizens: 64 KB buffers rented, not allocated). Rented memory is **not zeroed** — previous contents remain; clear if that matters.
- **2 — `finally Return`**: the Ch 14.2 gate-leak rule, for memory: a dropped rental removes the buffer from circulation *and* (worse) keeps the pool paying for it until GC. If the buffer escapes your scope (returned in the result), copy out or `Return` later — never double-return.

##### 24.8 Structs: small, readonly, `in`

```csharp
public readonly record struct Money(decimal Amount, string Currency);   // value type, immutable, equality
public static decimal Sum(in Money a, in Money b) => a.Amount + b.Amount; // `in`: passed by reference — no copy
```

Use structs for small (≲ 24–48 B), immutable, short-lived values — they die on the stack, free of GC. `readonly` prevents defensive copies; `in` avoids copying on calls; `record struct` gives equality/`ToString`. Do **not** make big structs (copying costs more than GC), mutable structs (aliasing bugs), or struct-in-collection-then-sort-by-interface (boxing).

##### 24.9 The honest decision: ShopX changes *nothing* this chapter

Run the profiler now (Ch 6 method) during load: per-request allocation is ~96 KB (E-20), and the call-tree attribution shows **application code owns under ~10% of it** — the rest is framework plumbing (Kestrel, EF command pipeline, MVC), which is already pooled or fast-pathed. Our allocation sins were *structural* (tracked entities, lazy proxies, reflection JSON, per-call HttpClients) — Parts II–VI removed them wholesale. Flattening LINQ or pooling builders inside the remaining 10% would buy nanoseconds and cost readability: exactly Knuth's "small efficiencies", exactly the trap Chapter 1 forbids.

So the scoreboard line for **LAB.md E-21** reads: *"No change. Evidence: profiler shows app-code allocation share <10%. Decision: apply Ch 24 patterns only where profiling justifies — re-audit in Ch 30."* Writing that line is the milestone most engineers never reach: **measured restraint**. The toolkit is now yours for the audit of some *other* codebase — the one that did not have this book.

#### Measuring the change

E-21 as above — a null result with evidence. If your own run attributes more to app code (different codebase!), the chapter's tables tell you which pattern applies where; benchmark each candidate before changing it.

#### Key takeaways

- Strings: buffer for loops (`StringBuilder` sized, `Clear()`+reuse on services, `string.Create`/`TryFormat`); interpolation is fine once-per-response, not per-row.
- Closures: `static` lambdas, watch `foreach` captures; ~64 B/call is real only in hot paths.
- LINQ: ~3 iterator objects + delegate per chain — flatten only where the profiler says; EF-side LINQ is translated, not executed (Ch 16).
- Boxing hides in `ViewData`/`ViewBag`/`object` slots; typed models and generic APIs remove it.
- `Span<T>`/`SearchValues`/`stackalloc`: zero-copy views — the per-row superpower; `ArrayPool`: rent-and-return with `finally`; structs: small, readonly, `in`.
- The mastery move: **measure, then decide not to** — recorded like any result.

#### Exercises

**Exercise 24.1 — Price the fix.** A profiler attributes 40% of allocations to `BuildLabel(item)` called per row (10k rows/pass): inside it, `$"{item.Sku} | {item.Name} | {item.Price:C}"` plus two `Substring` calls. Design and measure the fix.

*Solution.* Per call today: one interpolated string (compiles to Concat — several intermediate strings), two substrings (~40 B each), the `:C` format. Fix: a `StringBuilder` field on the service, `Clear()` once per pass, `Append` pieces (with `TryFormat` for the number into a stackalloc span); substrings → `AsSpan().Slice()` views consumed in place. Expected (illustrative): from ~200 B/row to ~0 allocated steady-state — 2 MB/pass saved. Then stop: the remaining allocation is the *result* string per row, which is the product — not a cost to eliminate.

**Exercise 24.2 — The over-engineering review.** A PR replaces every LINQ call in a 5k-file codebase with `for` loops "for performance", citing this chapter. Reject or accept, with reasons.

*Solution.* Reject. Chapter 24's rule is profiler-gated: most of those sites are cold (run rarely); the change buys nanoseconds there while costing readability, review surface, and bug risk (off-by-one in hand loops) — Knuth's 97%. The correct version: profile, list the top allocation sites by share, convert *those*, benchmark before/after, and leave the rest. This book's E-21 exists precisely so you can cite a written null-result in that review.

#### Interview questions

**Q: When would you NOT use `Span<T>`?**
A: When the code is not hot (measured), when the view must outlive the current scope (a field, an async boundary — use `Memory<T>`, not `Span`), or when the source is unmanaged/pinned and the safety contract is unclear. `Span` is a per-call-site tool, gated by the profiler; scattering it through cold code is readability damage for zero return.

**Q: How do `ArrayPool` and the GC interact?**
A: Pooling removes the buffer *allocations* (fewer Gen0/LOH citizens), so the GC sees less pressure — but the pool is now a long-lived root: rented-but-never-returned buffers are effectively a leak, and over-renting (huge buffers kept forever) trades GC pressure for steady-state memory. The contract (`Rent`/`Return` in `finally`, single ownership) is what keeps both sides honest.

**Q: What did ShopX Chapter 24 change, and why is that the right answer?**
A: Nothing — because the profiler showed application code owns <10% of remaining per-request allocation; the big allocation sins were structural and already removed (tracking, proxies, reflection, per-call clients). Applying micro-patterns there would trade readability for unmeasured nanoseconds. The general answer it demonstrates: allocation work is profiler-gated, structure before syntax, and "no change" with evidence is a legitimate engineering result.

#### Performance checklist

- [ ] I can list the seven patterns and their costs from memory
- [ ] My hot paths have no per-row closures/boxing/substrings (searched, not assumed)
- [ ] Pooled buffers follow Rent/`finally`-Return; rented memory treated as dirty
- [ ] LINQ flattened only where the profiler attributed share — with before/after benchmarks
- [ ] E-21 written: the null result with its evidence
- [ ] I have refused at least one micro-optimization *with a measurement* — the maturity gate

---
### Chapter 25: GC Modes and Runtime Knobs

#### What this chapter is about

Chapter 23 taught what the GC does; this chapter chooses *how it behaves*, and finishes the runtime story: JIT tiering/PGO (the free speedups), publish options (ReadyToRun, Native AOT), Kestrel limits, and container memory reality. Ends with the decision table for every deployment shape you will meet, plus ShopX's own publish measured.

#### A short history of this topic

Workstation and Server GC shipped with .NET 1.0 (2002): one tuned for interactive apps (low memory, one heap), one for throughput (a heap *per core*, collected in parallel — more RAM, more speed). Background/concurrent collection (2.0 → 3.5 era) shrank pauses. .NET 8 (Nov 2023) added **DATAS** — Dynamically Adapting To Allocations: server-grade throughput that scales its heap count down when your allocation rate does not need it. On the JIT side: tiered compilation (2.0, 2017) made code cheap-then-optimized, and **dynamic PGO became the default in .NET 8** — the JIT instruments hot code and recompiles with real branch data: a multi-percent runtime speedup that arrived as a version bump, no code changes (the Chapter 2 lesson, monetized).

#### What it provides you

- The knob table (csproj/runtimeconfig/environment), the Kestrel limits, and the publish flags — each with its "what it buys/what it costs".
- The container sizing rule that keeps you off the OOM-kill list.
- A defensible answer to "should this service be AOT?"

#### Why you should use it

Because these settings are *configuration as engineering*: wrong GC mode on a 512 Mi container = OOMKills at 3 a.m.; R2R missing = cold-start p95 that looks like an outage for the first minute; DATAS off on a big box = paying RAM for throughput you do not use. Ten minutes of configuration, decided once, with the table below.

##### 25.1 Workstation vs Server vs DATAS

| Mode | Heaps | RAM | Pauses/throughput | Right for |
|---|---|---|---|---|
| **Workstation** | 1 (+background) | low | lower throughput, small footprint | density hosts (many apps/box), small containers |
| **Server** | one per core | high (each heap has budgets) | highest throughput; pauses still short | dedicated app servers, big boxes |
| **DATAS** (.NET 8+) | starts server-like, **scales heaps down** when allocation rate does not need them | adapts | server-ish throughput, bounded footprint | the modern default candidate for server apps |

ASP.NET Core web templates set **Server GC on by default** — verify in your csproj (`<ServerGarbageCollection>`), because "default" has been wrong before and class libraries can flip it. Concurrent GC (`<ConcurrentGarbageCollection>`, default on) lets Gen2 sweep in the background — keep it on unless you need deterministic pauses (some game/robotics niches).

ShopX (illustrative check on the Ch 5-class load): with Server GC, CPU ~10% lower at the same rps vs Workstation on my 8-core box; memory ~2.5× higher. On a 512 Mi container that trade inverts — hence the table's container row.

##### 25.2 The knob table

| Knob | Where | Buys | Costs |
|---|---|---|---|
| `<ServerGarbageCollection>` | csproj | throughput (or DATAS's adaptive version) | RAM |
| `<ConcurrentGarbageCollection>` | csproj | shorter Gen2 pauses | a little CPU during collection |
| `<InvariantGlobalization>` | csproj | startup, ICU-less size | **all culture-specific formatting/collation** |
| `System.GC.DynamicAdaptationMode` | runtimeconfig / `DOTNET_GCDynamicAdaptationMode` | DATAS (pair with Server GC) | none measurable — try it |
| `DOTNET_GCHeapHardLimit` | env (hex bytes) | bounded heap in containers | GC works harder near the cap |
| `<TieredPGO>` | csproj (default **on** since .NET 8) | free steady-state speed | first-pass instrumentation overhead (tiny) |
| `<PublishReadyToRun>` | csproj/publish | faster startup (pre-compiled native code) | bigger binaries; steady state unchanged (tiering recompiles better code anyway) |
| `<PublishTrimmed>` | publish | smaller self-contained size | reflection breakage — needs testing |

**The `InvariantGlobalization` honesty check (do it in review, always):** it drops ICU for a culture-invariant mode — smaller, faster startup. But every culture-specific operation changes behavior: our views' `@price.ToString("C")` would render `¤` instead of a currency symbol, and `ToLowerInvariant`-style calls are fine but locale *names* are not. **ShopX keeps ICU** because changing displayed prices is a behavior change (Ch 1, test 1 — the rule applies to configuration exactly as to code). The flag is right for APIs that never format for humans; ours formats for humans. That paragraph, in a PR comment, is what a specialist's review looks like.

##### 25.3 Containers: the sizing rule

.NET 6+ reads cgroup limits: the GC sees the container's memory ceiling and behaves. But "sees the ceiling" is not "respects your headroom" — the .NET process also has non-heap memory (stacks, POH adjacency aside: JIT code, native buffers, connections). The rule teams learn from OOMKills:

> **Heap hard limit ≈ 60–75% of the container limit** — the rest is headroom for everything that is not the GC heap.

```yaml
# k8s-style, in whatever orchestrator you use
env:
  - name: DOTNET_GCHeapHardLimit
    value: "268435456"        # 256 MiB of a 512 MiB limit (~50% here — small box, generous headroom)
resources:
  limits: { memory: "512Mi" }
```

Workstation GC (or DATAS) at that size; Server GC's per-core heaps want ≥ ~1 GiB before they stop being a liability. Symptom of getting it wrong: the process grows to the limit, the kernel sends SIGKILL — no exception, no dump, "restart at 3 a.m." (Ch 29's alerting exists to catch the *growth before* the kill: heap size metric approaching the hard limit is the pager's trigger).

##### 25.4 Kestrel limits (and HTTP/3)

```csharp
builder.WebHost.ConfigureKestrel(o =>
{
    o.Limits.MaxConcurrentConnections = 1_000;        // default: unlimited — set it consciously
    o.Limits.MaxRequestBodySize = 1_048_576;          // default 30 MB; our API bodies are <1 KB
});
```

Defaults are honest for a lab; production should *choose*: an unlimited connection cap lets one hostile client hold sockets (pair with Ch 22's concurrency limiter); a 30 MB request body cap is an attack surface for a JSON API. `MinResponseDataRate` (exists, rarely tuned) protects slow readers. HTTP/3 is opt-in per-listener (`HttpProtocols.Http1AndHttp2AndHttp3`) — a real user win on lossy mobile links, a ops win only when your load balancer also speaks it; measure, do not cargo-cult.

##### 25.5 Publish: ReadyToRun, and the AOT question

```text
dotnet publish ShopX.Api -c Release -r win-x64 --self-contained false /p:PublishReadyToRun=true
```

- **ReadyToRun**: pre-compiles to native code at publish → startup skips much of the JIT. Measured on ShopX (illustrative): cold *first request* p95 ~220 ms → **~90 ms**; steady-state rps unchanged (within noise — tiering's Tier1 code is *better* than R2R code; R2R is a startup tool, not a throughput tool). Binary ~1.4× larger.
- **Native AOT**: whole program ahead-of-time — instant startup, small footprint, no JIT at all. Constraints: **no runtime code generation** — controllers (reflection-heavy MVC) are out; minimal APIs + source-gen JSON (Ch 11!) + no lazy proxies (Ch 16 removed ours!) are in. ShopX's API is *controller-shaped*, so AOT is honestly "no, not this app" — but notice which earlier chapters were quietly building the AOT-compatible list (source-gen, no proxies, typed DTOs). A greenfield hot microservice could take that path today.

##### 25.6 The deployment decision table

| Shape | GC | DATAS | Heap hard limit | Notes |
|---|---|---|---|---|
| Dev box | default (Server) | — | — | iterate fast; Ch 5's warmup discipline |
| Small container (≤ 512 Mi) | Workstation | optional | ~60–75% of limit | watch heap metric vs limit (Ch 29 alert) |
| Mid container (1 Gi) | Server or DATAS | **try** | ~70% | DATAS usually wins here |
| Big VM (8+ cores, dedicated) | Server + DATAS | yes | per-RAM budget | R2R publish for cold starts |
| Density host (many apps/box) | Workstation per app | no | per-app | footprint is the shared resource |

##### 25.7 Applying it to ShopX + measuring

Changes (both apps): nothing in csproj — the template's `<ServerGarbageCollection>true</ServerGarbageCollection>` is already right for a dedicated dev box; `InvariantGlobalization` explicitly *not* set (25.2's check); publish with R2R for the startup measurement. Then (illustrative, LAB.md **E-22**):

| Metric | JIT-only publish | R2R publish |
|---|---|---|
| Cold first request `/api/products` p95 | ~220 ms | **~90 ms** |
| Steady-state p95 / rps | baseline | unchanged (within noise) |

The mechanism line: *R2R trades binary size for first-pass code; tiering re-optimizes the hot set regardless — startup is what R2R buys.*

#### Measuring the change

E-22 as above, plus one screenshot-level habit for your real job: after any runtimeconfig/csproj change, re-run the Ch 5 load *once* — configuration is code, and it gets the same one-change-one-measurement rule (Ch 1) as any PR.

#### Key takeaways

- Server GC: heaps per core, throughput, RAM; Workstation: footprint; **DATAS** (.NET 8+) adapts server behavior to allocation rate — try it on mid-size+ deployments.
- Containers: heap hard limit ≈ 60–75% of the limit; small boxes want Workstation/DATAS; OOMKills are the symptom of skipping this section.
- `InvariantGlobalization` is a behavior change whenever anything formats for humans — ShopX keeps ICU, and that is the *review* answer, not trivia.
- R2R buys startup (measured ~2.4× first-request here), not steady state; AOT needs the reflection-free stack (minimal APIs + source-gen + no proxies — the list this book kept building).
- Every config change gets the Ch 1 treatment: one change, re-measured under the same load.

#### Exercises

**Exercise 25.1 — Size the box.** Service: 400 rps, ~120 KB alloc/request (that is ~48 MB/s allocation), steady heap ~380 MiB. Target container. Choose GC mode and limits for (a) a 512 Mi container; (b) a 1 Gi container.

*Solution.* (a) 512 Mi is *tight*: heap hard limit ≤ ~350 MiB is above steady heap but leaves no growth headroom for bursts (alloc rate spikes → heap spikes → GC thrash near the cap; OOMKill risk). Honest answer: do not run this on 512 Mi — or cut the allocation rate first (Ch 24's levers), which shrinks steady heap *and* burst amplitude. If forced: Workstation + hard limit 320 MiB + CPU-based throttling, and an alert at 280 MiB (Ch 29). (b) 1 Gi: Server GC + DATAS, hard limit ~700 MiB, alert at 600 MiB — comfortable. The transferable step is the *sequence*: steady heap → burst headroom → hard limit → alert threshold — all four numbers written down.

**Exercise 25.2 — The AOT decision.** A 40-endpoint controller API (ShopX-shaped) is proposed for Native AOT "for startup". Give the review.

*Solution.* Controllers are not AOT-compatible today (reflection-rooted model binding/MVC machinery); the proposal as stated fails at build. The honest review has three parts: (1) what is the actual goal — startup? R2R gets most of it (measured 25.7) without rewriting; (2) if AOT is truly required (CLI tooling, edge, cold-start SLO), the path is: minimal API endpoints + `JsonSerializerContext` source-gen (Ch 11) + no lazy loading/proxies (Ch 16) + trimming-annotated dependencies — i.e., a rewrite, priced honestly; (3) decide by measurement of *their* startup SLO, not by fashion. The pattern to keep: AOT-readiness is an architectural property accumulated chapter by chapter, not a switch.

#### Interview questions

**Q: Workstation vs Server GC — how do you choose in one sentence each?**
A: Workstation: one heap, small footprint — for shared/density hosts and small containers where RAM is the constraint; Server: heap per core, parallel collection — for dedicated app servers where throughput is the constraint; with DATAS (8+) in play, server behavior adapts down, which collapses most of the old decision into "try DATAS and measure".

**Q: Why is the first request after deploy slow, and what do you do about it?**
A: Cold costs: JIT compilation of the just-touched code paths, EF query-plan cache empty, output/object caches empty, connection pools unwarmed. Mitigations, in order of value: pre-warm with synthetic traffic before taking real load (Ch 28 canary), R2R for the JIT share (measured ~2.4× in 25.7), jittered TTLs so caches do not synchronize their refills (Ch 19), and readiness probes that gate traffic until warm (Ch 29). Never "fix" it by disabling tiering — steady state depends on Tier1/PGO code.

**Q: What does `DOTNET_GCHeapHardLimit` protect against, and what does it NOT?**
A: It caps the GC heap so the process does not grow into the container limit and get OOM-killed — it bounds *heap* memory. It does not bound non-heap memory (JIT code, native buffers, thread stacks, socket buffers), which is why the 60–75% rule leaves headroom, and why near the cap the GC works harder (more frequent, less efficient collections) — you trade OOMKills for latency, which is exactly the trade to size consciously and alert on (heap approaching hard limit).

#### Performance checklist

- [ ] csproj reviewed: Server GC explicit; `InvariantGlobalization` decided *with the formatting check*, not by fashion
- [ ] Deployment shape picked from the 25.6 table; DATAS tried and measured where applicable
- [ ] Container path (if any): hard limit set at 60–75% of the limit; alert threshold below it
- [ ] Kestrel limits chosen (conns, body size) — not left at defaults in production
- [ ] Publish flags: R2R for startup; first-request measured before/after (E-22)
- [ ] AOT compatibility list known (minimal APIs, source-gen, no proxies) — and decided, not drifted into

---
## Part VIII: MVC-Specific Optimization

The API is done; Part VIII finishes the storefront. Chapter 26 goes inside the Razor engine — how views execute, what partial views/view components/tag helpers actually cost, and fragment caching for the regions that page-level caching can never cover. Chapter 27 steps to the far side of the wire: page weight, asset caching, bundling, and Core Web Vitals — where we catch a surprise the profiler has been hiding in plain sight.

### Chapter 26: Razor Views and the View Pipeline

#### What this chapter is about

A `ViewResult` is executed by the Razor engine — compiled code, not interpreted templates. This chapter shows what that means (views are C# in your assembly; runtime compilation is a dev-only tax), prices the three reuse constructs (partial views, view components, tag helpers), and adds the last caching layer: **fragments** — for per-user pages and high-cardinality variants that whole-page output caching cannot afford.

#### A short history of this topic

Razor (2010, WebMatrix era) replaced the clunky `<% %>` Web Forms syntax with `@`-marked C# that reads like the page it produces. The big performance event was ASP.NET Core's decision to **compile views at build time** — `.cshtml` files become classes in your assembly (`.Views.dll` for libraries), executed as ordinary IL. `RazorRuntimeCompilation` (the package that recompiles views on file change) exists for design-time convenience and costs startup time, memory, and a watcher process — a dev feature that too many production apps still carry. Tag helpers (ASP.NET Core 1.0) moved markup generation into compiled C# as well: the `asp-*` attributes you met in Chapter 4 are *not* runtime reflection — they are compiled into the view.

#### What it provides you

- The cost model of view execution (lookup cached, execution compiled, rendering = string building through `IHtmlContent`).
- The partial/view-component/tag-helper decision table.
- `FragmentCache` — cached HTML regions with HybridCache semantics (single-flight, tags).
- The security rule that comes with `HtmlString`.

#### Why you should use it

Because the view is where the *page's* weight is decided (Chapter 27 catches ours), and because per-user pages (carts, dashboards) can never be output-cached whole — the regions *around* the personal data can be, and nobody teaches you where to cut.

##### 26.1 How a view executes

`return View(model)` → the view engine locates the view by path (lookup cached per action), instantiates the compiled class, and executes: your `@foreach` loops and tag helpers run as IL, building the response through `IHtmlContent` buffers (not `string +=` — the engine was allocation-aware before this book was). The model flows in as the typed `Model`. Everything is compiled: there is no parsing at request time. Consequences:

- **Never ship `RazorRuntimeCompilation` to production** — it exists so designers can hot-reload; in prod it is startup cost, memory, and a file watcher per app. Dev convenience, prod tax: remove the package from the publish configuration (or set `RazorCompileOnBuild` discipline and let CI fail if it sneaks in).
- View *lookup* is cached by the framework — you do not need to do anything.
- The work left in a view is *your* loops and *your* markup volume. Rendering cost ≈ proportional to emitted HTML size — which is why Chapter 27's page-weight work is also a render-time win.

##### 26.2 ViewData/ViewBag — already dead in ShopX, and why that was also a perf fix

Chapter 4 used `ViewBag.TotalPages` (dynamic dictionary, boxing every number — Ch 24.5's trap). Chapter 16 replaced it with typed records (`CatalogPage`, `CartLine`). That was not cosmetic: typed models remove boxing, remove dynamic-dispatch call sites, and give the compiler the view's contract. In review: `ViewBag` in a hot view is a smell with a measured cost; typed view models are the fix, and they were already shipped in Part IV. One paragraph here closes the ledger.

##### 26.3 The three reuse constructs

| Construct | What it is | Cost profile | Use for |
|---|---|---|---|
| **Partial view** (`PartialAsync("_Name", model)`) | a compiled view rendered inline | a `ViewDataDictionary` copy per render (real, per-partial) + execution | repeated markup fragments |
| **View component** (`Component.InvokeAsync`) | a mini-controller+view pair | slightly heavier discovery, async by design, cacheable output | widgets with logic/queries (a nav needing data) |
| **Tag helper** (`<a asp-action=…>`) | compiled attribute run during the host view's execution | negligible — it *is* part of the view's IL | markup ergonomics; URL generation |

Rule: markup reuse → partial; *behavior* reuse (needs its own data) → view component; syntax sugar → tag helper. None of them is a performance problem at our scale — choose for design, and let the profiler override (Ch 1, always).

##### 26.4 Fragment caching: the layer for what pages can't cache

Whole-page output cache (Ch 10) keys on the full URL — useless when the page mixes personal data with shared regions (the cart page: *your* quantities, *everyone's* product cards) or when key cardinality explodes (Ch 17's `page × category × sort` = thousands of variants). The fix is caching the *shared fragments* on their own keys. `ShopX.Web/Services/FragmentCache.cs`:

```csharp
using Microsoft.AspNetCore.Html;
using Microsoft.Extensions.Caching.Hybrid;

namespace ShopX.Web.Services;

public class FragmentCache(HybridCache cache)
{
    public async ValueTask<IHtmlContent> RenderAsync(
        string key, TimeSpan ttl, Func<Task<IHtmlContent>> render, CancellationToken ct = default)
    {
        var html = await cache.GetOrCreateAsync(
            key,
            async _ => (await render()).ToString(),                  // 1 — render once, store the string
            new HybridCacheEntryOptions { Expiration = ttl, LocalCacheExpiration = ttl },
            tags: ["fragments"],
            cancellationToken: ct);

        return new HtmlString(html);                                // 2
    }
}
```

Usage in a view (say the product card, shared across every category/sort variant):

```cshtml
@inject ShopX.Web.Services.FragmentCache Fragments
@await Fragments.RenderAsync(
        key: $"card:{p.Id}",
        ttl: TimeSpan.FromMinutes(5),
        render: () => Html.PartialAsync("_ProductCard", p))
```

- **1 — `(await render()).ToString()`**: the partial renders once; its HTML string is the cached value (strings cache cheaper than object graphs — and this is a *shared* cache, remember Ch 19's rules: fragments keyed on *shared* dimensions only: `card:{id}` is shareable, `cart-row:{owner}:{id}` would not be — per-user keys multiply entries by user count; cut at the personal boundary instead).
- **2 — `new HtmlString(html)`**: `IHtmlContent` that marks the string as *already encoded*. Razor will output it verbatim — **no re-encoding pass** (the win) and also the security contract: `HtmlString` must only ever wrap markup you generated yourself from trusted data; wrapping user input this way is an XSS hole. One rule, both directions.

What you get from HybridCache here (Ch 20's ledger, applied): single-flight per fragment key (two concurrent cold renders of the same card → one render), tag eviction (`RemoveByTagAsync("fragments")` on price changes), and — the actual point — **card fragments are shared across every page variant**: `card:42` is identical on `/catalog?page=1`, `?sort=price`, `/cart` — the cache works *across* the cardinality that page-level keys cannot span.

##### 26.5 Sections, layouts — the mechanics, briefly

`_Layout.cshtml` wraps `@RenderBody()`; `@RenderSection("scripts", required: false)` declares slots. All compiled; no per-request discovery cost worth discussing. The only layout-level perf notes that matter: keep the layout lean (it is paid on *every* page), and do not put per-user data in it (it would defeat output caching of every page — the Ch 10 safety rules, applied to chrome).

##### 26.6 Measuring it

Our honest scale: a card partial renders in well under a millisecond; twenty cards are single-digit milliseconds of the ~125 ms miss path (Ch 16's scoreboard). Fragment caching therefore buys **single-digit ms on output-cache misses with fresh variant keys** — real but modest at ShopX's size; at 4-digit page-variant cardinality (Ch 17's matrix) or on per-user pages it is the *only* layer that works at all. LAB.md **E-23**: render the catalog with 20 random `page+sort` variants twice; second pass should show `fragments` hits (Ch 18's counter pattern on the FragmentCache if you want numbers) and a few ms off X-Elapsed. Mechanism line: *fragments key on shared dimensions, so they hit across page-variant cardinality that page keys cannot.*

#### Measuring the change

E-23 as above. Also the ledger line: `RazorRuntimeCompilation` confirmed absent from both apps' publish — one grep, recorded.

#### Key takeaways

- Views are compiled IL (build-time); runtime compilation is a dev-only package — never in production; view lookup is framework-cached.
- Typed view models over `ViewBag` (boxing + dynamic dispatch) — already done in Ch 16; enforce in review.
- Partials = markup reuse (ViewData copy per render); view components = behavior; tag helpers = compiled sugar. Choose by design; override by profiler.
- `FragmentCache`: cache shared fragments on shared-dimension keys (single-flight + tags via HybridCache); `HtmlString` = already-encoded, trusted-markup-only.
- Layout is paid on every page: lean, and never per-user.

#### Exercises

**Exercise 26.1 — Cut the page.** The cart page needs: user's items (personal), product cards (shared), and a promo banner (shared, 5-min freshness). Where does each piece live, and why?

*Solution.* Items: rendered live per request (personal — never cached, the cart rule from Ch 19's table). Product cards: `FragmentCache` on `card:{productId}` — shared across users and pages; single-flight protects the 5-min refresh. Promo banner: fragment keyed `promo:active` with TTL 5 min + tag `fragments`, invalidated on publish — freshness by event, TTL as backstop. The page itself: never output-cached (it contains personal rows); its *shared* surroundings are. That boundary walk is the exam answer.

**Exercise 26.2 — The HtmlString audit.** A teammate caches a fragment containing `@Model.UserComment` (user text) and returns it as `HtmlString`. What happened, and what is the fix?

*Solution.* XSS: user content flowed into the fragment *unencoded* (the `HtmlString` wrap told Razor "trusted, pre-encoded" — so `<script>` in a comment executes for every user who sees that cached fragment, across all page variants). Fix: encode at the boundary — user text must pass through normal Razor encoding (`@comment`), which means it cannot live in a pre-encoded fragment; cache only the trusted shell and render personal/user content live, or encode explicitly (`HtmlEncoder.Default.Encode`) before storing. The rule: **`HtmlString` is for markup you generated, never for data you were handed.**

#### Interview questions

**Q: Are tag helpers slower than raw HTML?**
A: No — they compile into the view's C# (no runtime reflection); their cost is the code they generate (URL resolution for `asp-action`, etc.), which is what you would have written by hand. Choose them for ergonomics; the profiler is the only voice that can overrule, and it almost never does.

**Q: Why keep partials out of hot loops, and what's the alternative?**
A: Each `PartialAsync` copies the `ViewDataDictionary` and runs a separate compiled view — small per call, multiplied per row. Alternatives: render the fragment inline in the loop (markup duplication), or — for repeated identical fragments — `FragmentCache` (one render, N reuses). The first move is always measuring: partials at 20/row are fine; at 2,000/row they show up in the profile.

**Q: When is fragment caching the *only* layer that works?**
A: Per-user pages (personal rows make page keys impossible) and high-cardinality page variants (page × filter × sort matrices that would explode output-cache memory). Fragments key on shared dimensions only, so they hit *across* variants and users — the layer that spans cardinality page-level caches cannot.

#### Performance checklist

- [ ] No `RazorRuntimeCompilation` in any production publish (grep the csproj/publish profile)
- [ ] Zero `ViewBag`/`ViewData` in hot views (typed models only — Ch 16 already shipped this)
- [ ] Fragments: shared-dimension keys only; `HtmlString` wraps self-generated markup only (XSS rule)
- [ ] FragmentCache in place for card/promo regions; tags registered; single-flight verified (Ch 20 semantics)
- [ ] Layout lean and impersonal (it rides every page)
- [ ] E-23 in LAB.md: variant-matrix second-pass hit rates

---
### Chapter 27: Frontend Delivery for MVC Apps

#### What this chapter is about

The last mile is the browser's, but the server decides most of it: TTFB (already 40 ms — Part II), page weight, asset caching, and image markup. This chapter fixes the three of those still wrong in ShopX — and catches a surprise the profiler never showed us because it was never *in* the app: **our catalog page has been shipping a ~1,000-link pager on every render since Chapter 4.**

#### A short history of this topic

Front-end delivery optimization is a 2010s discipline born of page-weight panic: the average page passed megabytes as frameworks bloated; Google formalized the counter-metrics — Core Web Vitals (LCP 2020, then INP replacing FID in 2024) — and made them ranking signals. The tools you meet here: content fingerprinting (mainstream since Rails asset hashing, ~2010), `immutable` cache directive (part of HTTP caching semantics formalized mid-2010s), runtime bundlers in ASP.NET (WebOptimizer succeeding the Framework-era `BundleConfig` — Ch 2's history table promised this succession), and the `loading="lazy"` attribute (native in browsers since ~2019).

#### What it provides you

- The server-side control map of browser loading: what you decide, what the browser decides.
- Fingerprinted immutable asset caching (zero round trips for a year), windowed pagination (the page-weight catch), bundling/minification when the app grows, and the image rules to apply the day art arrives.
- Core Web Vitals literacy: LCP/CLS/INP and which of them the server can move.

#### Why you should use it

Because users do not experience your p95 — they experience *load*, and load is bytes + round trips. A 40 ms TTFB followed by 90 KB of mostly-pager HTML and a CSS file that revalidates on every visit is a fast backend wrapped in a slow front. Also: your output-cache store (Ch 10) holds every byte of every variant — page weight is a *memory* bill too.

##### 27.1 What the server controls

```
Browser: parse HTML ──► block on CSS (<link> in head) ──► build DOM ──► images load ──► LCP paints
           ▲                    ▲                          ▲
           │ HTML bytes         │ CSS bytes+cache         │ image bytes+markup
      TTFB + page weight   asset URLs & headers      width/height, loading
        (Parts II–V)        (this chapter)           (this chapter)
```

The server decides: HTML size, asset URLs + cache headers, image markup, script placement. The browser decides: parse/render speed, preloading hints' effects. Measure with DevTools (Network + Lighthouse tabs in Edge/Chrome; PSI for field data).

##### 27.2 The catch: page weight (the pager, not the products)

Look at the actual numbers (Network tab, or just the response size): our `/catalog` HTML is ~**86 KB** for twenty products worth ~4 KB of cards. The rest is the pager: `TotalPages = 20,000 / 20 = 1,000` → **1,000 `<a>` elements rendered on every page view** — since Chapter 4, invisible in every profiler because it is *render and bytes*, not CPU or queries. This is the classic page-weight bug: pagination UI that scales with data size.

The fix is a **windowed pager** — links around the current page, plus first/last and ellipses (~15 links instead of 1,000):

```cshtml
@{
    var current = Model.CurrentPage;
    var total   = Model.TotalPages;
    var from    = Math.Max(1, current - 5);
    var to      = Math.Min(total, current + 5);
}
<div class="pager">
    @if (current > 1)  { <a asp-action="Index" asp-route-page="1">«</a> }
    @if (from > 6)     { <span>…</span> }

    @for (var pg = from; pg <= to; pg++)
    {
        <a asp-action="Index" asp-route-page="@pg"
           class="@(pg == current ? "current" : "")">@pg</a>
    }

    @if (to < total - 5)  { <span>…</span> }
    @if (current < total) { <a asp-action="Index" asp-route-page="@total">»</a> }
</div>
```

(In your version, carry the current filter params through the links: `asp-route-category`, `asp-route-sort` — the view model already holds them; elided here for focus. And note what did *not* change: the data pagination is still keyset-friendly server-side — Ch 16.4; this is purely about markup volume. Deep jumps beyond the window can use a `?page=` input — an honest UX note, not a perf one.)

Measured (illustrative, my machine): HTML **86 KB → ~7 KB (−92%)**; render time in X-Elapsed drops a few ms; and the output-cache store shrinks ~10× for the same number of variants (Ch 10's budget line pays its first dividend). **LAB.md E-24**, mechanism: *markup volume must be O(window), not O(data size) — the same rule as keyset pagination, one layer up.*

##### 27.3 Static assets: fingerprint + immutable

Today (`_Layout.cshtml`): `<link rel="stylesheet" href="~/css/site.css" />` — no cache header, so the browser revalidates on every visit (a round trip + 304; the file is 2 KB). Two one-line changes:

**1. Fingerprint the URL** (tag helper, built in):

```html
<link rel="stylesheet" href="~/css/site.css" asp-append-version="true" />
```

`asp-append-version` hashes the file content into the URL: `site.css?v=abc123…`. When the file changes, the hash changes, the URL changes — one cache miss, ever, per deploy.

**2. Tell every cache it may keep it forever** (`Program.cs`, Web):

```csharp
app.UseStaticFiles(new StaticFileOptions
{
    OnPrepareResponse = ctx =>
        ctx.Context.Response.Headers.CacheControl = "public,max-age=31536000,immutable";
});
```

`OnPrepareResponse` runs just before each static-file response (Ch 8's `OnStarting` cousin, at the middleware); `immutable` says "this URL's content will never change" — so browsers skip even the revalidation round trip for a year. Correct *because* of step 1: the URL itself encodes the version. Without fingerprinting, `immutable` would serve stale CSS after a deploy — the two lines are one mechanism.

Measured (illustrative): second+ visits make **zero** requests for CSS (DevTools Network: "(disk cache)"); first visit per deploy downloads once. Before: one 304 round trip per visit, forever.

##### 27.4 Bundling and minification (when the app grows)

With one 2 KB CSS file, bundling buys nothing measurable — say so in LAB.md and skip it (Ch 24's discipline, applied to the front end). The pattern for when assets multiply (5 CSS + 8 JS files = 13 round trips): bundle them. Runtime option in pure MVC — **WebOptimizer** (NuGet: `WebOptimizer.Core`):

```csharp
builder.Services.AddWebOptimizer(o =>
{
    o.BundleCssFiles("/css/site.min.css", "css/site.css");
});
app.UseWebOptimizer();
```

and point the layout at `site.min.css`. It bundles + minifies at runtime with caching — the modern successor to the Framework's `BundleConfig` (Ch 2's succession, kept). For build-time control (CI, no runtime cost), any bundler works (esbuild, Lightning CSS); the MVC-native path above is the lowest-friction one. The round-trip math is the reason this exists: N files = N requests (HTTP/1.1) or N streams (HTTP/2) — bundles reduce *round trips*, which is what slow networks actually charge for.

##### 27.5 Images — the rules to apply the day art arrives

ShopX ships no binaries (a text catalog); when real products get photos, these are the rules (generic patterns, no binaries needed to learn them):

- **Always `width` and `height` attributes** (or CSS aspect-ratio): reserves space, kills layout shift (CLS).
- **`loading="lazy"`** on below-the-fold images: the browser defers the fetch — free bytes saved on long pages.
- **`srcset`/`sizes`**: serve appropriately-sized files per screen — a phone does not need the 1600 px master.
- Modern formats (WebP/AVIF) via `<picture>`: ~30–50% smaller at equal quality (widely supported; measure *your* users).
- The one measurable trap: **the LCP element** (usually the hero/first product image) must *not* be lazy-loaded, and should be prioritized (`fetchpriority="high"` on modern browsers).

##### 27.6 Core Web Vitals: what the server can move

| Vital | Measures | Server influence |
|---|---|---|
| **LCP** (Largest Contentful Paint) | when the biggest visible element paints; "good" < 2.5 s | **strong**: TTFB + page weight + image rules — everything in this chapter and Part II |
| **CLS** (Cumulative Layout Shift) | visual stability; "good" < 0.1 | image `width`/`height`, no late-injected content — markup discipline |
| **INP** (Interaction to Next Paint) | responsiveness to input; "good" < 200 ms | mostly client JS; server contributes via payload size (less JS to parse) |

Check with Lighthouse (Edge DevTools) — expect the *shape*: ShopX's LCP on a dev box is dominated by paint of a small page; on a real 4G profile, page weight and TTFB dominate — which is exactly what E-24's numbers bought. Field data (real users) comes from Chrome UX Report/PageSpeed Insights — Chapter 29's monitoring closes that loop with RUM once you ship.

##### 27.7 Measuring it

| Metric (illustrative) | Before Ch 27 | After |
|---|---|---|
| `/catalog` HTML (miss path) | ~86 KB | **~7 KB** (−92%) |
| Output-cache store for same variants | ~10× larger | shrunk ~10× |
| CSS requests per visit (2nd+) | 1 × 304 round trip | **0** (disk cache, immutable) |
| Pager links rendered | 1,000 | **~15** |
| LCP (Lighthouse, simulated slow 4G, dev box — treat as shape, not truth) | ~3.9 s | **~1.2 s** |

**LAB.md E-24** with the LCP number explicitly labeled *environment-dependent* — front-end measurements vary by machine more than anything else in this book; the mechanism lines (bytes, round trips) are the transferable content.

#### Measuring the change

E-24 as above. The honest ledger: bundling **not** applied (one 2 KB file — recorded as a decision, not an oversight); images deferred until art exists (rules pre-registered above); pager and asset caching measured and shipped.

#### Key takeaways

- Page weight is markup discipline: **UI must be O(window), never O(data size)** — the pager catch generalized to every list-like UI.
- Assets: fingerprint (`asp-append-version`) + `Cache-Control: immutable, max-age=1y` = zero round trips between deploys; the pair is one mechanism.
- Bundling reduces round trips; apply when file counts grow (WebOptimizer or build-time) — measuring first, skipping honestly when small.
- Images: dimensions (CLS), lazy below fold (bytes), srcset (right size), the LCP element never lazy.
- Core Web Vitals: LCP is the server's vital (TTFB + weight); CLS is markup discipline; INP is mostly client JS.

#### Exercises

**Exercise 27.1 — Page-weight audit.** A dashboard renders 200 rows × 40 cells, each cell with a nested `<div class="badge">` — page weight 620 KB, render 180 ms. Diagnose and fix.

*Solution.* Two costs: bytes (620 KB) and DOM size (200 × 40 × 2 ≈ 16k nodes — parse/parse/layout, which the *browser* pays; LCP and INP suffer). Fixes in order: (1) window the rows (O(window) — this chapter's rule); (2) shrink per-row markup (the badge div is a styling concern — CSS `::before` removes a node per cell); (3) virtualize if all 200 must scroll (client technique — mention, don't build). Expected (illustrative): 25 rows/window → ~80 KB, render ~40 ms — the rest of the table streams as pages. Mechanism: *bytes and nodes scale with visible content, not with data size.*

**Exercise 27.2 — The immutable trap.** A teammate sets `Cache-Control: immutable, max-age=31536000` on `/css/site.css` *without* fingerprinting. Walk the failure.

*Solution.* Deploy day: CSS changes on disk, but every returning browser holds the old file for a year *without even revalidating* — the site renders with yesterday's broken styles, and "hard refresh" becomes the unofficial support channel. The fix is the pair from 27.3: fingerprint (`asp-append-version`) so the URL changes with content, *then* `immutable` is truthful. The general rule: **`immutable` is a claim about a URL, and only versioned URLs can make it.**

#### Interview questions

**Q: How do you make static assets cache as aggressively as possible without breaking deploys?**
A: Content-fingerprint the URL (hash in the query/path) + `Cache-Control: public, max-age=31536000, immutable`. Content change → new hash → new URL → one miss per deploy; between deploys, zero round trips (not even 304s). Never use `immutable` on unversioned URLs; keep HTML itself always fresh (`no-cache`/short TTL) so the *references* roll first.

**Q: What is LCP and how does the server improve it?**
A: Largest Contentful Paint — when the largest visible element (hero image/heading/text block) paints; good < 2.5 s. Server levers: TTFB (Part II), HTML weight (markup discipline — this chapter), critical asset ordering (CSS blocks render — keep it small and early; make the LCP image high-priority, never lazy), and image sizing (srcset). Client levers (fonts, JS) matter, but on server-rendered MVC the TTFB+weight pair dominates.

**Q: Your MVC page is fast server-side (40 ms) but Lighthouse scores poorly. What order do you investigate?**
A: Page weight first (Network tab: bytes by type — markup volume, image masters), then round trips (file counts, revalidations — 27.3's 304 pattern), then render-blocking resources (CSS/JS placement), then CLS causes (unsized images, late content), and only then micro JS issues. Server-side X-Elapsed being good is necessary, never sufficient — the browser's waterfall is a separate budget (Ch 3's TTFB note, completed).

#### Performance checklist

- [ ] Pager (and every list UI) is O(window): pager links ~15, not 1,000 — measured in E-24
- [ ] Assets: `asp-append-version` + immutable headers; zero 304s on repeat visits (DevTools verified)
- [ ] Bundling decided *by file count*, recorded (applied or skipped honestly)
- [ ] Image rules pre-registered (dimensions, lazy, srcset, LCP-not-lazy) for when art arrives
- [ ] Lighthouse run on /catalog before/after; LCP noted as environment-dependent shape
- [ ] Page-weight line item added to Ch 19's policy table thinking (cache store sizes follow page weight)

---
## Part IX: Production and Mastery

The book's last movement. Chapter 28 upgrades your load testing from "ramp some VUs" to open-model arrival rates that find true capacity, and runs ShopX's final exam. Chapter 29 wires in OpenTelemetry, health checks, and the alerts that watch p95 while you sleep. Chapter 30 is the capstone: the 10-step audit method, the complete before/after scoreboard, the top-30 pitfalls, and a 20-question mastery exam — the certificate you print for yourself.

### Chapter 28: Load Testing Like an Engineer

#### What this chapter is about

Chapter 5 taught the k6 basics; this chapter is the methodology that separates defensible numbers from theater: **closed vs open load models**, coordinated omission, thresholds as CI gates, and the reading of saturation. Then the full-optimized ShopX runs its final load test — the book's headline numbers.

#### A short history of this topic

Fixed-VU (closed-model) testing came from the bench-testing era: N users, each in a loop. Gil Tene's "coordinated omission" talks (2013–2015) exposed the flaw: with closed model, *the load generator slows down when the system slows* — victims of a stall are simply not generated, and your percentile is computed only over the requests that happened to be sent — you measure a flattering fiction. Open-model testing (constant arrival rate — k6's `constant-arrival-rate`, like Gatlin's hold-the-rate, and the way real users arrive: independent of your health) measures what users actually experience: stalls queue *in the system*, and the percentiles show it. k6 (Grafana Labs) made scenario-based open models a first-class feature; NBomber brings the same ideas to .NET-native tests.

#### What it provides you

- Model literacy: when closed is fine (soak, UX simulation) and when open is mandatory (capacity, SLA claims).
- The final ShopX run with honest warm/cold split, and the capacity point where p95 breaks.

#### Why you should use it

Because your production traffic does not slow down when your app does — it piles up. If your load test cannot reproduce that, every "we tested at 500 rps" claim in your org is untested at the only moment that mattered.

##### 28.1 Closed vs open, and the omission trap

- **Closed model** (N fixed VUs, think time): each VU sends, waits for the response, sleeps, repeats. When the system stalls, VUs stall too — fewer requests are issued *exactly when the system is at its worst*. Percentiles computed from that run systematically miss the stall's victims: **coordinated omission**. Fine for soak tests and realistic single-user journeys; wrong for capacity claims.
- **Open model** (fixed arrival rate): requests are generated at rate λ *regardless* of response times. Stalls now queue in the system (as they would in production), and the percentiles include every victim. This is the model for "what happens at peak".

```js
// capacity.js — open model: hold 300 req/s regardless of latency
import http from 'k6/http';
export const options = {
  scenarios: {
    steady: {
      executor: 'constant-arrival-rate',
      rate: 300, timeUnit: '1s',            // 300 requests per second, sustained
      duration: '3m',
      preAllocatedVUs: 100, maxVUs: 600,    // enough VUs to *deliver* 300/s even if latency rises
    },
  },
  thresholds: {
    'http_req_duration{name:api-products}': ['p(95)<100'],
    'http_req_failed': ['rate<0.001'],
  },
};
export default function () {
    const page = 1 + (__ITER % 10);
    http.get(`http://localhost:5080/api/products?page=${page}`, { tags: { name: 'api-products' } });
    http.get(`http://localhost:5180/catalog?page=${page}`,       { tags: { name: 'web-catalog' } });
}
```

Read `rate/timeUnit` as "λ per second, held for the duration". `maxVUs 600` is the anti-omission insurance: if latency doubles, there are still enough VUs to keep *issuing* 300/s. Thresholds fail the run (exit code ≠ 0) — this is the CI gate: `k6 run capacity.js && deploy` or the pipeline breaks.

##### 28.2 Reading a capacity run

Sweep the rate (100 → 300 → 600 → 1,000/s, separate runs, E-25 entries each). Three signatures to read, in order:

1. **Throughput plateau**: issued rate 1,000/s but completed 820/s — the system is shedding (errors/queueing) somewhere: check counters (ThreadPool queue — Ch 12; CPU — Ch 6; connection limits — Kestrel/Ch 25; rate limiter 429s — Ch 22, which *should* engage at the configured cap and is a designed stall, not a failure).
2. **p95 knee**: the rate where p95 leaves the linear region and doubles per step — that knee *is* your capacity point. Below it, headroom; above it, lying to yourself.
3. **Warm/cold split** (Ch 9's discipline): report both. Users at 9 a.m. Monday after a deploy are all cold; users at lunch are warm. One number without the label is not an answer.

##### 28.3 The tuning loop, formalized

Every chapter of this book executed one turn of this loop; write it once on the whiteboard and keep it:

```
measure (E-##) ─► hypothesize (mechanism, one sentence) ─► change (ONE thing)
     ▲                                                            │
     └────────── verify (same load, same data) ◄───────────────────┘
                    │
                    ▼
              record in LAB.md (verdict + mechanism)
```

The reason each arrow exists is somewhere in Chapters 1–5; if you ever skip one, you know which chapter to reread.

##### 28.4 The final run (all optimizations in)

One `capacity.js` pass at the knee-searching rates, plus the cache-busted miss-path run (Ch 9's `_r` trick) — illustrative results from my machine, the book's final scoreboard rows:

| Metric (steady, 50–300 rps sweeps) | Baseline (Ch 5) | Final |
|---|---|---|
| `/api/products` p95 — **warm** (hits) | 471 ms | **24 ms** |
| `/api/products` p95 — **miss** (cache-busted) | 471 ms | **~90 ms** |
| `/catalog` p95 — warm / miss | 540 ms | **~40 / ~110 ms** |
| Capacity knee (single app instance, p95<100 ms gate) | ~35 rps | **~900 rps** |
| Errors at knee | 0% | 0.01% (spike-run 429s reported separately — Ch 22) |

Read the capacity row twice: that is **~25× the throughput at a 20× better p95** — the compound interest of Parts II–VIII. Not one change did this; ~15 one-change experiments did, each with its LAB entry. That sentence is the book's thesis in scoreboard form.

##### 28.5 NBomber (one paragraph)

If your team prefers .NET-native load tests (shared solution, C# scenarios, no JS), NBomber is the established choice — same open/closed concepts (`Step`, scenarios, `Simulation.KeepConstant`/`InjectConstant`), same thresholds, xunit-friendly. The methodology of this chapter maps 1:1; pick by team taste, not by benchmark of the benchmarkers.

#### Measuring the change

E-25: your sweep table (rate → p50/p95/errors/counters) with the knee marked. If your knee is where ours is, you are done with Part IX's first half; the next chapter makes sure someone is watching these numbers at 3 a.m.

#### Key takeaways

- Closed model = VUs slow with the system (omission — never for capacity claims); **open model = constant arrival** is the truth test; `maxVUs` buys enough generators to keep issuing.
- Sweep rates; read the plateau, the p95 knee, and the warm/cold split; the knee is the capacity, the rest is headroom.
- Thresholds are CI gates (non-zero exit on breach) — wire them into the pipeline.
- The tuning loop (measure → hypothesize → one change → verify → record) is the book in five arrows.
- Final ShopX: p95 471→24 ms warm, ~25× capacity at the p95 gate — the compound of ~15 single-change experiments.

#### Exercises

**Exercise 28.1 — Model the launch.** Marketing will email 200k users at 10:00; click-through arrives at ~1,500/s for 60 s, then decays. Design the test (model, rates, durations, what to watch).

*Solution.* Open model, ramping-arrival-rate: ramp 0→1,500/s over 5 s, hold 60 s, decay to 200/s over 10 min. Watch: p95 knee position vs 1,500/s (if the knee is below, you will see it here — *this* run is the forecast); error split (429s by design vs 5xx not by design); warm/cold is all-cold at T0 (caches empty — include one cold-start pass on purpose; Ch 25's R2R and pre-warm are the mitigations, and their effect shows in this test); counters (queue length, CPU, heap). Run it twice: once with output-cache cold, once warm — the delta is your cache dividend, stated honestly.

**Exercise 28.2 — Find the omission.** A closed-model soak test reports p95 80 ms at "200 rps" while production (same hardware) shows p95 900 ms at 200 rps from real users. Explain both numbers.

*Solution.* The soak test's 200 rps was *achieved* only while the system was healthy — when it stalled, the VUs stopped issuing (omission), so the 80 ms is a survivor's average; production's users arrive at 200 rps regardless of health (open model by physics), so the stall's victims queue and land in p95 → 900 ms. The test was answering "how fast are good moments?", the dashboard answers "how fast is the system?" — different questions, and only the second one pages anyone. Fix: re-test open-model at the arrival rate; expect it to reproduce the 900 ms and then find the knee.

#### Interview questions

**Q: What is coordinated omission and how do you avoid it?**
A: Under closed-model load, the generator pauses when the system stalls, so stalled-interval victims are never issued and percentiles are computed over survivors — an optimistic fiction. Avoid it by testing open-model (constant arrival rate, enough pre-allocated VUs to keep issuing during stalls), reporting warm/cold separately, and cross-checking load-test percentiles against production telemetry (Ch 29) — if they disagree, the load test is usually the liar.

**Q: How do you find a system's capacity?**
A: Open-model rate sweeps with a stated SLO gate (e.g. p95 < 100 ms, errors < 0.1%): ramp through rates, watch for the knee — the rate where p95 leaves linearity — and the throughput plateau where issued ≠ completed. Capacity = the knee with headroom policy on top (run at 50–70% of it); document the warm/cold split, because post-deploy traffic is all-cold.

**Q: Why do thresholds in the load script matter beyond the run itself?**
A: They turn the test into a gate: a breached threshold exits non-zero, so CI/CD refuses to ship a regression — the same number that would have been a production incident becomes a red pipeline. Pair with per-tag thresholds so each endpoint carries its own SLO, and keep spike/protection tests' 429s counted as checks, not failures (Ch 22's discipline).

#### Performance checklist

- [ ] I can explain closed vs open models and the omission trap in two sentences
- [ ] Capacity sweep done; knee marked in E-25 with warm/cold split
- [ ] Thresholds wired as CI gates (exit codes verified by breaking one on purpose)
- [ ] The tuning loop is on my wall / notes — each arrow traced to its chapter
- [ ] Final scoreboard rows recorded from *my* machine, honestly labeled illustrative
- [ ] Spike tests report 429s separately from failures

---
### Chapter 29: Observability with OpenTelemetry

#### What this chapter is about

Everything so far was measured in the lab; production is where p95s are born at 3 a.m. This chapter wires ShopX into **OpenTelemetry** — traces (where time went, per request, in production), metrics (the counters you already met, plus the framework's), and health checks with the rules that keep them honest — then the alerting philosophy that watches the numbers so you do not have to. No infrastructure required: the lab runs on the console exporter; the production path (OTLP → Grafana/Aspire dashboard) is explained conceptually, exactly like Chapter 20's Redis.

#### A short history of this topic

The three pillars (traces, metrics, logs) consolidated in the 2010s from Google's Dapper paper (2010) and the SRE book's alerting doctrine. OpenTelemetry (2019 merger of OpenTracing + OpenCensus; CNCF project) is the vendor-neutral standard: one instrumentation API, any backend (Grafana/Tempo, Jaeger, Application Insights, Honeycomb, Datadog — your choice, switchable, which is the point). .NET's integration is first-class: `System.Diagnostics.Metrics` (which Chapter 18 already used) *is* the .NET face of OTel metrics; ASP.NET Core itself emits standard metrics since .NET 8. If you buy one concept from this chapter: **instrument once, in the standard, and every dashboard you will ever buy can read it.**

#### What this provides you

- Distributed traces on every request (including per-SQL-query spans), with console proof in the lab and an OTLP switch for production.
- Your existing `ShopX.*` meters flowing into the same pipeline.
- Health checks that obey their rules (never cached, never rate-limited, short-circuit early), and alert thresholds that page on symptoms, not noise.

#### Why you should use it

Because "it was fast in the lab" (Chapters 5–28) and "it is fast in production" are different claims, and only the second one pays. And because the 3 a.m. question — *which layer is slow right now* — is answered by a trace in seconds, versus hours of guessing without one. Observability is the performance specialist's pension plan: it makes every future optimization measurable.

##### 29.1 Traces: wiring and reading

Install into both apps (NuGet, latest stable):

```text
OpenTelemetry.Extensions.Hosting
OpenTelemetry.Instrumentation.AspNetCore
OpenTelemetry.Instrumentation.Http
OpenTelemetry.Instrumentation.SqlClient
OpenTelemetry.Exporter.Console          (lab; swap for OpenTelemetry.Exporter.OpenTelemetryProtocol in prod)
```

Registration (Api `Program.cs`; mirror in Web):

```csharp
builder.Services.AddOpenTelemetry()
    .WithTracing(t => t                                    // 1
        .AddSource("ShopX.Api")                            // 2 — our own activities/meters, by name
        .AddAspNetCoreInstrumentation(o =>                  // 3 — every request becomes a span
            o.Filter = ctx => !ctx.Request.Path.StartsWithSegments("/healthz"))   // 4
        .AddHttpClientInstrumentation()                     // 5 — outbound calls (reviews!)
        .AddSqlClientInstrumentation()                      // 6 — every EF query becomes a child span
        .AddConsoleExporter())                             // 7 — lab output
    .WithMetrics(m => m
        .AddMeter("ShopX.Api")                             // Ch 18's counters, by name
        .AddAspNetCoreInstrumentation()
        .AddHttpClientInstrumentation()
        .AddConsoleExporter());
```

- **1 — `WithTracing` / `WithMetrics`**: the two pipelines; each is a set of *producers* (sources/instrumentations) and *exporters* (sinks).
- **2/`AddMeter("ShopX.Api")`**: this is the continuity moment — the cache hit/miss counters from Chapter 18 and any `Activity` you add are selected *by the name you gave the Meter*. Nothing else changes.
- **3 — `AddAspNetCoreInstrumentation`**: every inbound request becomes a span (the trace's unit: named, timed, nested). **4** — the `Filter` excludes `/healthz` (below) from tracing: probes every 10 s would drown the trace volume — a cost decision, made once.
- **5/6 — Http + SqlClient instrumentation**: outbound HTTP and every `DbCommand` get their own child spans automatically — the reviews fan-out and each EF query appear in the trace with zero code.
- **7 — console exporter**: prints spans/metrics to stdout — perfect for the lab; in production, swap to `.AddOtlpExporter()` (the OTLP package) pointing at your collector/Grafana stack, or the Aspire dashboard — *the same registration, one line different*.

Run the Api, hit `/api/products?page=1` twice (cold, then warm), and read the console. What the trace shows (illustrative, annotated):

```
ShopX.Api GET /api/products (15.2 ms)                     ← the request span
  ├─ shopx.db SELECT COUNT(*)            (1.1 ms)
  ├─ shopx.db SELECT ... OFFSET ...      (2.3 ms)
  ├─ System.Text.Json (from X-Elapsed split)              ← visible as the un-spanned remainder
warm (output-cache hit):
ShopX.Api GET /api/products (0.4 ms)                      ← no children at all — the cache short-circuit
```

That second shape — *a request span with no children* — is the visual proof of everything Part II did: no SQL spans because no query ran; the span duration ≈ the `X-Elapsed-Ms` you have had since Chapter 8. In 2026-era ShopX-baseline (Ch 4 code), the same trace would show **twenty-two** `SELECT TOP(1)` children — you can no longer write that app by accident; you would *see* it.

##### 29.2 Metrics: the ones that page someone

Already flowing: `shopx.cache.*` (Ch 18) and ASP.NET's built-ins (request duration histograms — that is where production p95 comes from, per endpoint, no load tester involved). Add the two custom ones that earn their keep in incidents, from Chapter 23's list: GC heap size (alert as it approaches the hard limit — Ch 25's rule) and ThreadPool queue length (the Ch 12 fingerprint). Histograms (request duration) are pre-aggregated into p50/p95/p99 by your backend — never ship raw samples to a dashboard.

##### 29.3 Health checks: with their rules

Package: `Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore`. Registration:

```csharp
builder.Services.AddHealthChecks()
    .AddDbContextCheck<ShopXContext>("database");          // SELECT 1 under the hood
```

```csharp
app.MapHealthChecks("/healthz");                           // endpoint routing: short-circuits nothing else
```

The rules (each one is a Ch 8/10/22 principle, now applied to the probe itself):

1. **Never cached** (Ch 10 safety): a cached probe reports yesterday's health — exclude it from output cache; it is excluded from tracing here by the same logic (29.1's `Filter`).
2. **Never rate-limited** (Ch 22): `[DisableRateLimiting]`-equivalent placement; your autoscaler must always reach the probe, even mid-attack — a throttled probe is how you *scale into* an outage.
3. **Split liveness/readiness** if you run Kubernetes: liveness = "process is up" (no dependencies — restarting is the response), readiness = "dependencies usable" (the DbContext check; route traffic away, do not restart). One probe doing both jobs restarts boxes that merely lost a dependency, and ships traffic to boxes that cannot serve — both classic outages.

##### 29.4 Alerting: page on symptoms, with a budget

The SLO doctrine in one paragraph: pick an SLO per endpoint (e.g. `GET /api/products` p95 < 100 ms over 5 minutes, 99.9% of the time). The gap between 100% and the SLO is your **error budget** — the allowed badness. Burn it on releases and risks; when it is spent, freeze features and fix. Alerts fire on *symptoms users feel* (p95 breach, error rate, health check failing), each with a runbook link; never on causes (CPU is 80%) — causes are dashboards, not pages. Two rules that keep 3 a.m. quiet: page only on what a human can act on *now*, and route every alert to a place where silence is visible (the "unowned alert" is how systems rot).

The GC heap rule from Ch 25 rides here: `heap_size / hard_limit > 0.85 for 5m → page` — the alert *before* the OOMKill, which itself never pages (the process is dead; Ch 25's warning that you need the *growth* signal).

##### 29.5 Diagnosing in production, from your chair

- `dotnet-trace collect -p <pid> --profile cpu-sampling` (Ch 6) — same tooling, live process, container or VM.
- `dotnet-gcdump collect` twice during a leak-shaped growth (Ch 23's diff) — no restart required.
- Logs: production level `Warning`+ (request logging via the framework's built-in `Microsoft.AspNetCore` category at `Information` is fine and cheap — but never log per-row in hot paths; a log line is an allocation + I/O, Ch 24's rules apply to logging too).

#### Measuring the change

**LAB.md E-26**: paste (a) the cold and warm `/api/products` traces (the no-children shape is the proof), (b) the metrics console block showing cache hit-ratio counters, (c) a screenshot-level note of your alert thresholds. The scoreboard row this chapter buys is not milliseconds — it is *coverage*: every request now explains itself in production.

#### Key takeaways

- OpenTelemetry = vendor-neutral standard; `System.Diagnostics.Metrics` is its .NET face — Ch 18's meters already speak it; instrument once, read anywhere.
- Traces: request span + automatic SQL/HTTP children; a warm request with *no children* is the Part II proof visible in production; filters exclude probes from tracing volume.
- Health checks: never cached, never throttled, liveness/readiness split; a mistreated probe is an outage generator.
- Alerting: SLOs + error budget; page on symptoms (p95, errors, health) with runbooks; the GC-heap-vs-hard-limit alert fires *before* the OOMKill.
- Prod diagnosis is Ch 6's tools on live processes (`dotnet-trace`, `dotnet-gcdump`), plus the traces you already shipped.

#### Exercises

**Exercise 29.1 — Read the 3 a.m. trace.** Alert: `/api/products` p95 800 ms (SLO 100). One trace shows: request span 780 ms → child `http POST reviews` 620 ms (retrying? no — single span) → child `sql SELECT` 40 ms; CPU normal; other endpoints healthy. Diagnosis?

*Solution.* The time is in the *outbound* call (reviews), not the database and not your code — a downstream slowdown. The single 620 ms span says one call took 620 ms (not retries; retries would show as several short spans — Ch 21's handler would surface them). Next actions: the reviews service's own metrics (its p95), the circuit breaker's state (opened? Ch 21), and whether this endpoint *must* call reviews synchronously — the architectural fix is to cache/inline it (Ch 19's policy table: reviews row, TTL minutes) so a slow downstream stops owning your p95. One trace, one diagnosis, no guessing — that is the chapter's whole value.

**Exercise 29.2 — The probe that lied.** Kubernetes: one readiness probe `/healthz` doing `AddDbContextCheck` + output cache accidentally covers it. Describe both failure modes.

*Solution.* (1) Cached: after a DB outage begins, previously-cached probe responses keep saying "healthy" — Kubernetes routes traffic to pods that cannot serve (user-facing errors while the dashboard is green). (2) When the cache finally expires mid-outage, the probe flips *unhealthy* for **all** pods simultaneously → mass removal → thundering re-add on recovery (herd, again). The fixes are the three rules: probe excluded from caching (and from rate limiting), liveness split from readiness (DB check belongs to readiness only — liveness restarting pods cannot fix a database). Probes are infrastructure's load test: they must obey the same physics as Ch 22's "don't throttle your instruments".

#### Interview questions

**Q: Why OpenTelemetry rather than vendor X's SDK?**
A: Instrumentation is the expensive part (code + org habit); backends are the cheap, switchable part. OTel standardizes the former so you are never locked: same spans/metrics to Grafana today, Application Insights or anything else next year, and .NET's `Metrics`/`Activity` are already the OTel shapes — you may already be emitting without a vendor package.

**Q: What do you alert on, and what do you refuse to alert on?**
A: On symptoms users feel within minutes: p95/p99 per endpoint vs SLO, error rate, health checks failing, GC heap approaching its hard limit (pre-OOM). Refuse: causes with no user impact (CPU 80%, disk 60%), anything without a runbook, and anything a human cannot act on at the hour it fires. Every alert must have an owner; unowned alerts train people to ignore pages — the deadliest failure mode.

**Q: How do you measure production p95 without a load tester?**
A: Request-duration histograms from ASP.NET's built-in metrics (or OTel's aspnetcore instrumentation), aggregated per endpoint per window by the metrics backend — real traffic is the open model by definition (Ch 28's argument, completed): it includes every stall the load test would have omitted. Load tests find capacity; production histograms report truth; both, labeled, is the honest posture.

#### Performance checklist

- [ ] Both apps: tracing (aspnetcore/http/sqlclient) + metrics (ShopX.* + built-ins) wired; console in lab, OTLP noted for prod
- [ ] `/healthz` live with DbContext check; excluded from tracing, caching, rate limiting — by rule, not luck
- [ ] Liveness/readiness split documented (if orchestrating); probe rules on the review checklist
- [ ] Alert thresholds written with runbooks; GC-heap-vs-limit alert exists (the pre-OOM one)
- [ ] E-26 traces pasted: the warm no-children shape is there — the book's journey, visible in one span tree
- [ ] Prod log level policy decided (Warning+; no per-row logging in hot paths)

---
### Chapter 30: The Capstone Audit and Mastery Exam

#### What this chapter is about

The book's final chapter assembles everything into two deliverables you will use for the rest of your career: **the 10-step performance audit method** (run it on any codebase, including ones that never read this book), and **the mastery exam** — 20 interview-grade questions with model answers, one per major topic. In between: ShopX's complete before/after scoreboard with chapter-by-chapter credit, the honest ledger of targets amended by data, the top-30 pitfalls ranked, and the template for auditing *your* application.

#### What it provides you

- The transferable method — this chapter works even on a codebase you have never seen.
- The final scoreboard (the number that proves the method).
- The pitfalls list you will scan against every PR for the rest of your life.

#### Why you should use it

Because from tomorrow, the question is no longer "can you optimize ShopX?" — it is "can you walk into a stranger's codebase and make it measurably better without breaking it?" The audit method is that skill, written down.

##### 30.1 The 10-step audit method

1. **Define the target with the owners.** Which journeys, which SLO (p95/p99), which environments. No number, no audit (Ch 1/3).
2. **Baseline under load.** Open-model at realistic arrival rates; warm/cold split recorded; counters attached (Ch 28 + 5).
3. **Attribute, don't guess.** Profiler on the hot path: CPU vs waiting vs allocations (Ch 6's tool table). One sentence per layer: "p95 = 300 ms EF (22 queries) + 40 ms JSON + rest plumbing."
4. **Classify the dominant cost.** Data access / serialization / pipeline / blocking / memory / network — it picks the book part (the Ch 1 map).
5. **Attack structure before syntax.** Caching and projection delete work; micro-tuning shaves it (Parts II–V vs Ch 24's restraint).
6. **One change at a time; verify each.** Same load, same data, same machine; LAB entry with mechanism (Ch 1's rules — non-negotiable).
7. **Fix the write paths' correctness while you are there.** Invalidation with the change, not after (Ch 10.3/20.1); transactions atomic (Ch 4's `Place`).
8. **Protect the system.** Rate limits, resilience handlers, health rules, cache-safety review (Ch 22/21/29/19).
9. **Prove capacity and ship instrumentation.** Rate sweep to the knee; traces/metrics/alerts live before the feature flag flips (Ch 28/29).
10. **Write the report.** Before/after table, amended targets with reasons, what was *deliberately not done* (Ch 24's null results are results), and the next architecture when tweaks run out.

##### 30.2 The complete ShopX scoreboard

Every number illustrative from my machine (front-matter disclaimer — your lab notebook holds yours); every improvement traced to the chapter that bought it:

| Metric | Ch 5 baseline | Final (Ch 30) | Credit (chapters) |
|---|---|---|---|
| `/api/products` p95 — warm | 471 ms | **24 ms** | 10 (output cache), 11 (source-gen JSON), 16 (projection: payload 16.8→5.1 KB) |
| `/api/products` p95 — miss | 471 ms | **~90 ms** | 15–16 (22→1 query, no tracking/proxies), 17 (covering index on browse), 11 |
| EF queries / request | 22 | **0 (hit) / 1 (miss)** | 15 (mechanism), 16 (projection) |
| Allocations / request | 215 KB | **96 KB** | 16 (no snapshots/proxies), 11 (source-gen); 24: *deliberately no further* (evidence recorded) |
| JSON payload (raw / brotli) | 16.8 KB / 16.8 KB | **5.1 / ~1.5 KB** | 16 (DTO projection), 11 (compression) |
| `/catalog` HTML | ~86 KB | **~7 KB** | 27 (windowed pager: O(window), not O(data)) |
| `/catalog` p95 — warm / miss | 540 ms | **~40 / ~110 ms** | 10, 16, 27 |
| Cart page X-Elapsed | ~410 ms | **~90 ms** | 14 (WhenAll fan-out), 16 (projection), 21 (pooled client) |
| TIME_WAIT sockets under load | 6,100+ | **< 150** | 21 (typed client + pooling) |
| Cold first request (after deploy) | ~220 ms | **~90 ms** | 25 (ReadyToRun) |
| Capacity at p95<100 ms gate | ~35 rps | **~900 rps** | compound of all the above |
| Spike protection | none | 429s, allowed-traffic p95 flat | 22 |
| Gen2 GCs under sustained load | — (healthy by then) | **0** | 23 (allocation discipline via structure), 25 (mode verified) |

Read the last two rows together: ~25× capacity at a 20× better warm p95, achieved by ~15 one-change experiments — never by one heroic rewrite. That is the thesis, in table form.

##### 30.3 The honest ledger: targets amended by data

Written exactly as your reports should read them:

- **p95 < 100 ms (list, warm): MET** (24 ms). Miss path: ~90 ms — met.
- **≤ 1 query/request: MET** (0 warm / 1 miss).
- **Errors < 0.1%: MET.**
- **Allocations < 40 KB/request: NOT MET — amended to 96 KB.** The Ch 3 target was a pre-measurement guess. After Parts II–VI removed the app's structural share (215→96), the remaining floor is framework-dominated (controller stack + EF plumbing at this page size — profiler-attributed in Ch 24). The honest amendment: 96 KB with the evidence, plus the *next architecture* noted (minimal API + AOT, Ch 25.5's compatibility list) as the path below 40 — a rewrite decision, priced, not a tweak. Targets are hypotheses; data amends them; the amendment is written down or the number was theater.
- **`/catalog` p95 < 100 ms: MET warm (~40); miss ~110 — accepted with mechanism** (1 query + render floor at 7 KB HTML); flagged for the next round.

That ledger, more than the wins, is what a specialist's report sounds like: wins, misses, mechanisms, and the next decision — no spin.

##### 30.4 The top 30 pitfalls, ranked

Scanned against every PR; each maps to its chapter:

1. N+1 via lazy loading (15–16) · 2. Tracked reads that never save (15) · 3. `new HttpClient()` per call (21) · 4. Missing output/response cache on public GETs (10) · 5. Caching authorized/personalized content (10/19) · 6. `.Result`/`Thread.Sleep` in request paths (12) · 7. Sequential awaits on independent I/O (14) · 8. Missing CancellationToken plumbing (13–14) · 9. Averages reported as latency (3) · 10. Closed-model capacity claims (28) · 11. Unbounded fan-out hammering a dependency (14) · 12. No invalidation story (write-time) (10/19) · 13. Synchronized TTLs → stampede (19) · 14. Full entities serialized where DTOs belong (16) · 15. `ViewBag` boxing in hot views (26) · 16. OFFSET pagination at unbounded depth (16) · 17. Non-SARGable predicates (functions/wildcards on columns) (17) · 18. Missing covering index on the hot shape (17) · 19. Ignoring parameter sniffing symptoms (17) · 20. Middleware order accidents: caching before auth; limiter after cache (8/22) · 21. Compressing <1 KB / already-compressed payloads (11) · 22. Un-fingerprinted "immutable" assets (27) · 23. UI volume that scales with data size (the 1,000-link pager shape) (27) · 24. Unbounded endpoints buffering whole tables (16 — stream!) · 25. Unbounded in-process caches (no SizeLimit) (18/23) · 26. Per-row logging in hot paths (29/24) · 27. `async void`; bare fire-and-forget (14) · 28. No warm/cold split in any performance claim (5/28) · 29. Health probes that are cached/throttled (29) · 30. Optimizing without a baseline — the meta-pitfall this entire book exists to delete (1).

##### 30.5 Audit *your* application (the template)

Copy into `AUDIT.md` and fill — the template is the deliverable:

```markdown
# Performance audit — <app> (<date>)
## 1. Targets (with owners):  <journey> p95 < … ms; SLO; error budget
## 2. Baseline:  warm p50/p95/p99=…; cold …; rps …; counters (queue, alloc/req, Gen2) …
## 3. Attribution:  <one sentence per layer>
## 4. Dominant cost class:  data / serialization / blocking / memory / network
## 5. Plan (one change each, ordered by expected value):
## 6. Experiments:  E-1 … E-n (change → before → after → mechanism)
## 7. Protection review:  cache-safety ✓/✗ · invalidation ✓/✗ · rate limits ✓/✗ · health rules ✓/✗
## 8. Capacity:  knee @ … rps (open model, warm/cold split)
## 9. Instrumentation:  traces/metrics/alerts live? thresholds with runbooks?
## 10. Report:  before/after table · amended targets with reasons · deliberately-not-done · next architecture
```

##### 30.6 The mastery exam (20 questions, model answers)

Score yourself: 16+ correct (without peeking) = you are the specialist this book promised; 12–15 = reread the cited chapters; below 12 = the book worked less than you did — go build something and come back.

1. **Why is p95 preferred over the mean?** Long-tail distributions hide their tail in the mean; p95 bounds the experience of 1-in-20 requests — where SLAs and users live (Ch 3).
2. **State Little's Law and one capacity use.** L = λ×W; at fixed concurrency, cutting per-request time multiplies sustainable rate — sizing workers for target rps (Ch 3/12).
3. **What did ASP.NET Core remove that changes `.Result` behavior?** The per-request SynchronizationContext: no deadlock, but blocking still starves the pool — both eras, same rule, different mechanism (Ch 2/12/13).
4. **Where does a lazy load fire in our stack?** On navigation property *read* — during JSON serialization or Razor rendering (Ch 4/15).
5. **The N+1 fix, one sentence.** Move the child access into the parent query's shape: projection (best) or `Include` — one round trip instead of N (Ch 16).
6. **Projection vs `Include` for a read path?** Projection: fewer columns, no tracking, smaller payload, source-gen-friendly; `Include` is for write paths that will save the graph (Ch 16).
7. **When is keyset pagination mandatory?** Unbounded depth (infinite scroll/deep APIs) — OFFSET cost grows linearly; keyset seeks; needs a stable unique key (Ch 16).
8. **What does a Key Lookup operator tell you?** The index matches the predicate but not the SELECT — add the columns (covering index: equality → order → INCLUDE) (Ch 17).
9. **Name two non-SARGable patterns.** `LIKE '%x'`; any function wrapped around the column (`UPPER(Name)=`, `YEAR(d)=` → use ranges) (Ch 17).
10. **Parameter sniffing: symptom in one line.** Same query, different speeds by parameter value, flipping after restart/flush — cached plan fit one cardinality (Ch 17).
11. **Output cache vs response cache, one line each.** Server-side whole-response store with policies/tags/eviction vs HTTP-header semantics for browsers/CDNs (plus a small local store) (Ch 9/10).
12. **The three cache-safety rules you never break.** Never authorized/personalized content; never `Set-Cookie` responses; vary on every output-shaping input (Ch 10/19).
13. **What is a stampede and the first defense?** Synchronized expiry → N concurrent fills at peak; single-flight first (built into OutputCache/HybridCache) (Ch 18/19).
14. **Why does `ValueTask` exist on hot cache paths?** Sync-completion hits allocate nothing — the fast path monetized (Ch 13/20).
15. **What does ThreadPool Queue Length ≈ 5,000 with CPU 15% mean?** Blocking starvation — find `.Result`/`Sleep`/sync I/O (Ch 12's fingerprint).
16. **Why never `new HttpClient()` per call?** Per-call connection + kernel TIME_WAIT retention → port exhaustion at load; pool via typed clients (Ch 21).
17. **What does allocation rate predict?** GC frequency and tail pauses: halve allocations → halve collections (Ch 23).
18. **R2R vs AOT in one line each.** R2R: pre-compiled first-pass code — startup win, steady state unchanged; AOT: no JIT at all — needs the reflection-free stack, instant start (Ch 25).
19. **Closed vs open load model?** Closed: VUs slow with the system (coordinated omission — never for capacity); open: fixed arrivals — the truth test (Ch 28).
20. **What do you alert on, and never?** Symptoms (p95 vs SLO, errors, health, heap→hard limit) with runbooks; never causes without user impact, never unowned pages (Ch 29).

##### 30.7 What next

- **Run the audit** (30.5) on a real codebase — yours, or open-source; the template does not care whose app it is.
- **Read the sources** of the things you now trust: the output-cache middleware, HybridCache, EF's query pipeline. Appendix C links the docs; the code is in `dotnet/aspnetcore` and `dotnet/efcore` on GitHub — you now read them fluently.
- **Keep the lab notebook forever.** Every entry is a mechanism you own; superstition is what happens when the entries stop.
- **Teach one chapter.** Present Ch 12's experiment or Ch 18's stampede demo to your team — teaching is the exam after the exam.

#### Measuring the change

This chapter's measurement is the reader's: the exam score (30.6) and the first filled `AUDIT.md`. ShopX's numbers are closed — the E-01…E-26 ledger is the artifact; the method is the product.

#### Key takeaways

- The audit method: targets → baseline → attribution → classify → structure first → one change each → protect → capacity → instrument → honest report.
- Final scoreboard: warm p95 471→24 ms, ~25× capacity, 0-1 queries, 96 KB/request — via ~15 single-change experiments, never a rewrite.
- Targets are hypotheses; **amended targets with reasons** are professionalism (the 40 KB amendment is the example).
- The 30 pitfalls are your PR scan pattern; the exam is your self-check; the template is your first deliverable on any new codebase.

#### Exercises

**Exercise 30.1 — The stranger's codebase.** You are handed a service with zero documentation: p95 complaints, no metrics, no tests. Write your first week.

*Solution.* Day 1: scope with owners — one number (journey × SLO) agreed; install counters/health (Ch 29) — you cannot tune what you cannot see, and neither can they. Day 2–3: baseline + profiler attribution (Ch 5/6); expect the usual suspects (top-30 list) but *let the profiler vote*. Day 4: the three highest-value one-change experiments (structure first: caching/projection before anything clever); LAB entries with mechanisms. Day 5: report — before/after, amended targets, deliberately-not-done, and the audit template handed over so week two continues without you being a bottleneck. The transferable core: measurement before opinion, in that order, every day.

**Exercise 30.2 — Present the results.** Write the three sentences you would say to (a) the CTO, (b) the engineering team, (c) yourself, about this book's outcome.

*Solution.* (a) CTO: "Warm page latency improved ~20× and capacity ~25× at the p95 gate, from ~15 verified one-line-to-one-day changes; no feature changed; the full evidence ledger is in LAB.md." (b) Team: "We removed structural waste (lazy loads, per-call clients, reflection, page weight); the remaining costs are attributed and bounded; the pitfalls list is now our PR checklist." (c) Yourself: "Every number has a mechanism; every target has a ledger; the method works on code I have not met yet — that was the goal."

#### Interview questions

**Q: You join a team with a slow service and no measurements. What are your first three moves?**
A: (1) Agree one target number with owners (journey × percentile × environment); (2) stand up measurement — counters, health, a baseline load test with warm/cold split; (3) profiler attribution into one sentence per layer, then attack structure (caching/projection) before syntax. Move fast by measuring, not by guessing — the Ch 30.1 order.

**Q: How do you report a missed performance target?**
A: With the same format as a met one: the number, the mechanism that bounded it, the evidence (profiler attribution), what it would take (next architecture, priced), and the deliberate non-changes. Never silent massaging — the 40 KB amendment in 30.3 is the template: misses are data, and data is the job.

**Q: What is the single highest-leverage habit from this entire book?**
A: The lab notebook discipline: measure before, one change, verify after, write the mechanism. Every chapter of this book was one turn of that loop — and it is the only part that transfers to any technology you will touch next.

#### Performance checklist

- [ ] Mastery exam taken cold; score recorded; cited chapters reread where missed
- [ ] `AUDIT.md` template saved; first target codebase chosen
- [ ] The 30-pitfalls list lives where PRs are reviewed
- [ ] LAB.md ledger complete E-01…E-26 with mechanisms
- [ ] The report written in the 30.3 honest-ledger format — wins, misses, mechanisms, next
- [ ] One chapter scheduled to be taught to the team

---
## Appendices

The reference layer: everything you will want to look up faster than you can recall it.

### Appendix A: Cheat Sheets

#### A.1 The final middleware order (derivable, not memorized)

```text
1  UseExceptionHandler          catch everything below; outermost-ish
2  UseHsts                      (production only)
3  ElapsedMsMiddleware          instrumentation (Ch 8)
4  UseStaticFiles               short-circuits; before anything expensive
5  UseRouting                   endpoint resolution must precede endpoint-aware stages
6  UseRateLimiter               before caches — 429s must never be cached (Ch 22)
7  UseResponseCompression       wraps the producers; re-compresses cache hits per request (Ch 11)
8  UseResponseCaching           HTTP/browser semantics (Ch 9)
9  UseOutputCache               server-side, endpoint-aware (Ch 10)
10 UseAuthentication            as late as correctness allows
11 UseAuthorization
```

Rules that generate it: cheap short-circuits early · endpoint-aware after `UseRouting` · guards before caches · auth after anonymous-safe content · compression wraps producers.

#### A.2 Runtime knobs (csproj / runtimeconfig / env)

| Knob | Where | Buys |
|---|---|---|
| `<ServerGarbageCollection>` | csproj | throughput (heaps per core); web templates default true |
| `<ConcurrentGarbageCollection>` | csproj | shorter Gen2 pauses (default on) |
| DATAS | `System.GC.DynamicAdaptationMode` (runtimeconfig) / `DOTNET_GCDynamicAdaptationMode` | adaptive server GC (.NET 8+) |
| Heap hard limit | `DOTNET_GCHeapHardLimit` (≈60–75% of container limit) | no OOMKills |
| `<InvariantGlobalization>` | csproj | size/startup — **behavior change if anything formats for humans** |
| `<TieredPGO>` | csproj (default on, .NET 8+) | steady-state speed, free |
| `<PublishReadyToRun>` | publish | startup (~2.4× first request, measured Ch 25) |
| `<PublishTrimmed>` | publish | size — test reflection breakage |
| Kestrel | `ConfigureKestrel(o => o.Limits.…)` | MaxConcurrentConnections, MaxRequestBodySize |

#### A.3 Tool commands

```text
dotnet-counters monitor -n ShopX.Api --counters System.Runtime,Microsoft.AspNetCore.Hosting
dotnet-trace collect -n ShopX.Api --profile cpu-sampling -d 30
dotnet-gcdump collect -n ShopX.Api        &&  dotnet-gcdump report <file>
dotnet-dump collect -n ShopX.Api          →   dotnet-dump analyze <file>  (clrstack / dumpheap -stat / gcroot)
dotnet tool install --global dotnet-counters   (also: dotnet-trace, dotnet-gcdump, dotnet-dump)
dotnet run -c Release --project ShopX.Benchmarks -- --filter * --job short      (BenchmarkDotNet)
k6 run loadtest.js                       ·  k6 run -e TARGET=blocking experiments.js
dotnet publish ShopX.Api -c Release -r win-x64 --self-contained false /p:PublishReadyToRun=true
netstat -an | find /c "TIME_WAIT"        (Windows; socket-exhaustion evidence, Ch 21)
```

#### A.4 Counters quick-reference

| Counter | Healthy | Alarm | Chapter |
|---|---|---|---|
| CPU Usage | <70% sustained | pegged with low rps → CPU path | 6 |
| Alloc Rate | low MB/s; ÷rps = KB/req | climbing per request | 5/23 |
| Gen 0 GC Count | low single digits/s | — | 23 |
| Gen 2 GC Count | ~0 | **sustained >0** | 23 |
| GC Heap Size | flat between loads | growth that never returns | 23 |
| ThreadPool Queue Length | 0–low | thousands (blocking fingerprint) | 12 |
| http_req_duration (per tag) | p95 vs SLO | breach → CI gate | 5/28 |

#### A.5 The cache decision tree + policy table columns

```
personalized? ──yes──► per-request/per-user store, never one shared key
money-accurate? ──yes──► display cache (labeled) only; truth from source at transaction time
staleness cost visible? ──yes──► event/tag invalidation + TTL backstop
shared & read-heavy? ──yes──► highest layer that can serve it
otherwise ──► measure first; cache only if the number says so
```
Policy table columns: data · layer · TTL (+jitter) · invalidation trigger · staleness bound · **memory budget**.

#### A.6 The EF query checklist

`AsNoTracking` on reads · projection before `ToListAsync` (never after) · `Where` on bare columns (SARGable) · vary sets reviewed like schemas · keyset for unbounded depth · `AsSplitQuery` for parent+collections · unbounded endpoints stream (`AsAsyncEnumerable`) · one `SaveChanges` per logical transaction · bulk writes via `ExecuteUpdate/Delete` · `LogTo` when in doubt — count the statements.

#### A.7 Final ShopX architecture

```
Browser ─► CDN/edge (Cache-Control, Ch 9)                    public responses
        ─► Kestrel (limits, Ch 25) → middleware chart (A.1)
              ├─ RateLimiter (Ch 22)      protects capacity
              ├─ Compression (Ch 11)      −74% wire
              ├─ OutputCache (Ch 10)      whole responses: 0.4 ms hits
              ├─ Routing/Binding → Controllers (typed clients, Ch 21)
              │     └─ EF Core: projection + AsNoTracking (Ch 16) → covering indexes (Ch 17)
              │           └─ HybridCache L1 (Ch 20)  [+L2 Redis when scale-out, by the tree]
              └─ Fragments (Ch 26) for per-user pages' shared regions
        ─► OTel traces/metrics → alerts on p95 & heap-vs-limit (Ch 29)
```

### Appendix B: Glossary

**Alloc rate** — bytes allocated per second; the GC's workload dial (Ch 23). · **AOT** — ahead-of-time compilation; no JIT, instant startup, reflection-free stack required (Ch 25). · **Arrival rate (λ)** — requests per second offered to the system (Ch 3/28). · **AsNoTracking** — EF read mode: no snapshots/identity map (Ch 15). · **Brotli** — compression algorithm; ~15–25% better than gzip on text (Ch 11). · **Cache-aside** — read cache; on miss fill from source (Ch 19). · **CATAS/DATAS** — Dynamically Adapting To Allocations; server-GC behavior that scales heaps down (Ch 25). · **Coordinated omission** — load-test flaw where the generator slows with the system, hiding stall victims (Ch 28). · **Covering index** — contains all columns a query touches; no key lookups (Ch 17). · **DTO** — data transfer object; the projected read shape (Ch 16). · **ETag** — opaque validator for 304 revalidation (Ch 9). · **Error budget** — allowed badness under an SLO; spend on risk, freeze when spent (Ch 29). · **Fast path** — an `await` on already-completed work: no suspension, near-zero cost (Ch 13). · **Fingerprinting** — content hash in the asset URL; makes `immutable` truthful (Ch 27). · **Fragment caching** — cached HTML regions on shared-dimension keys (Ch 26). · **Gen0/1/2** — GC generations; die-young budget, buffer, expensive full (Ch 23). · **Hill climbing** — the thread pool's experiment-and-converge sizing (Ch 12). · **Hot path** — the small code fraction where time concentrates; the only place worth optimizing (Ch 1). · **HybridCache** — two-tier cache API: L1 objects, L2 bytes, single-flight, tags (Ch 20). · **Identity map** — tracked entities indexed by key within a context (Ch 15). · **Keyset pagination** — `WHERE key > @last`; constant cost at any depth (Ch 16). · **Kestrel** — ASP.NET Core's web server; owns the socket (Ch 2/7). · **L1/L2** — in-process cache tier / shared (distributed) tier (Ch 20). · **Lazy loading** — navigation fetch on first property read; the N+1 generator (Ch 4/15). · **LCP/CLS/INP** — Core Web Vitals: largest paint, layout stability, interaction latency (Ch 27). · **Little's Law** — L = λ×W (Ch 3). · **LOH** — large object heap; ≥85,000 B allocations, not compacted by default (Ch 23). · **Materialization** — row → object conversion in EF (Ch 15). · **N+1** — 1 parent query + N child queries from loops/serializers (Ch 15). · **Open model** — load at fixed arrivals regardless of system health (Ch 28). · **Output cache** — server-side whole-response store with policies/tags (Ch 10). · **p95** — 95% of requests were faster; the tail that users meet (Ch 3). · **Parameter sniffing** — plan cached for first-seen parameter's cardinality (Ch 17). · **PGO** — profile-guided optimization; dynamic PGO on by default in .NET 8+ (Ch 25). · **POH** — pinned object heap (Ch 23). · **Projection** — `Select` into the wanted shape, inside the query (Ch 16). · **ReadyToRun** — pre-compiled first-pass IL; startup win (Ch 25). · **SARGable** — predicate shape an index can serve (bare columns) (Ch 17). · **SemaphoreSlim gate** — bounded concurrency; single-flight building block (Ch 14/18). · **Single-flight** — one filler; waiters reuse its result (Ch 18/19). · **SLO** — service level objective; the number alerts serve (Ch 29). · **Span<T>** — a zero-copy view over memory (Ch 24). · **Stampede** — synchronized misses → N concurrent fills (Ch 18/19). · **State machine (async)** — the compiler's generated suspend/resume structure (Ch 13). · **SynchronizationContext** — marshaling context; per-request on Framework, absent on Core (Ch 13). · **TTFB** — time to first byte (Ch 3). · **Tiered JIT** — compile cheap, recompile hot with PGO (Ch 25). · **TIME_WAIT** — kernel-held socket state after close; the port-exhaustion cause (Ch 21). · **ValueTask** — result-or-task struct; zero-alloc sync completion (Ch 13). · **Vary** — cache separation dimension (query/header) (Ch 9/10). · **Workstation GC** — single-heap mode for footprint (Ch 25).

### Appendix C: Further Reading

Official documentation (stable URLs):

- ASP.NET Core performance best practices — https://learn.microsoft.com/aspnet/core/fundamentals/best-practices
- EF Core querying — https://learn.microsoft.com/ef/core/querying/ · EF Core performance — https://learn.microsoft.com/ef/core/performance/
- Output caching — https://learn.microsoft.com/aspnet/core/performance/caching/output · HybridCache — https://learn.microsoft.com/aspnet/core/performance/caching/hybrid
- Response compression — https://learn.microsoft.com/aspnet/core/performance/response-compression
- Rate limiting — https://learn.microsoft.com/aspnet/core/performance/rate-limiting
- HttpClient factory guidance — https://learn.microsoft.com/aspnet/core/fundamentals/http-requests
- GC basics & configuration — https://learn.microsoft.com/dotnet/standard/garbage-collection/ · runtime config options — https://learn.microsoft.com/dotnet/core/runtime-config/
- EF migrations (the production path we replaced with `EnsureCreated` in the lab) — https://learn.microsoft.com/ef/core/managing-schemas/migrations/
- C# language tour (if Chapter 4 moved too fast) — https://learn.microsoft.com/dotnet/csharp/tour-of-csharp/
- Health checks — https://learn.microsoft.com/aspnet/core/host-and-deploy/health-checks
- OpenTelemetry .NET — https://opentelemetry.io/docs/languages/net/ · k6 docs — https://k6.io/docs/ · BenchmarkDotNet — https://benchmarkdotnet.org/

Tools & community references used in the book:

- PerfView (free trace analysis) — https://github.com/microsoft/perfview
- TechEmpower web framework benchmarks (the .NET performance story, in public) — https://www.techempower.com/benchmarks/
- Ola Hallengren's maintenance scripts (statistics/index maintenance standard) — https://ola.hallengren.com/
- The .NET and ASP.NET Core source (read what you now trust) — https://github.com/dotnet/aspnetcore · https://github.com/dotnet/efcore

**The path forward.** In order: (1) run the Chapter 30 audit on a real codebase; (2) keep LAB.md going — it is the career; (3) read one framework source file a week (start with the output-cache middleware); (4) when you outgrow tweaks, the next architecture is on the table with evidence: minimal API + AOT, HTTP/3, an L2 tier, a search engine for the wildcard cases. Every one of those decisions was taught as a *decision tree* in this book — that is the graduation gift: you no longer need this book to answer "what should we do next", because you now ask "what does the measurement say?" first. Welcome to the specialist tier.
