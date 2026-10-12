---
date: '2026-10-08'
draft: false
title: 'CodeLab: Secure Assigment Marking System'
featureimage: "codelab-feature.png"

heroStyle: "background"

showTableOfContents: true


---

# AI-Free Assignment Mode: how Codelab protects a coding attempt

## A coding assessment built for authorship

In this day and age. AI usage during assigmnets and lab tasks has become the biggest issue for educators. though we can't eliminate the problem as a whole, we attempt to solve the part of it.Codelab is a web-based assigment submisstion platform whose central feature is **AI-Free Assignment Mode** a controlled assignment workflow for classes that want students to type, test, save, and submit their own C/C++ work in one place.

In today's world no browser can prove that a student has never used outside assistance. Codelab focuses on the most direct forms of transfer—clipboard insertion, drag-and-drop, browser automation, and runtime tampering andthen gives the teacher a coherent record to review. so even if a student is copying a solution he writes has to press every keystroke by himself.
![output](/login.png "CodeLab Login Page")



# Quick-start tutorial
Codelab has three modules : teacher, student and admin.

### Administrator: prepare the class structure

1. Sign in to the administrator area.
2. Create or review teacher accounts.
3. Select a teacher's name to open their classes.
4. Open a class to review its enrolled students and roll-number-sorted roster.
5. Use account settings to manage your own password and review the institution's legal and cookie notices.
![output](/admin.png "Snippet of Admin Dashboard")

### Teacher: publish an assignment

1. Sign in and open **Classes** or **Assignments**.
2. Create a class if one does not exist, then import students using the roster CSV. The target class controls enrollment; the CSV header is reference information and section text is not used to override that selection.
3. Choose **Assign assignment** from a class.
4. Upload the complete assignment as a PDF or DOCX. Set the due date, enter the number of questions, and select any additional owned classes that should receive the same assignment.
5. Publish the assignment. Codelab creates one blank C/C++ question tab per configured question for every enrolled student.
6. Open an assignment name to reach submissions. Select a student to open their first question, review the code and compiler output, and move through the question tabs.
7. Use the activity panel to review timestamps, typing signals, focus/fullscreen events, blocked actions, and tamper evidence. Enter per-question marks, then use the **Grades** action when you need to confirm or edit the assignment total.
![output](/teacher.png "Snippet of Teacher Dashboard")

### Student: complete an assignment

1. Sign in and open **Dashboard**, **My Classes**, or **Assignments**.
2. Choose the class and assignment, then select **Start** when you are ready. Starting opens the protected coding workspace.
3. Read the assignment document in the side panel and choose a question tab. Each question has its own Monaco editor.
4. Type your C/C++ solution in the editor. Use **Compile & Run** to test it; provide standard input in the terminal area when the program needs input.
5. Move between questions as needed. Work is autosaved, so a refresh or approved resume can restore the latest draft.
6. Submit when all required questions are ready. If the due time arrives first, remaining saved work is submitted automatically by the supported workflow.
7. Return to the assignment or class view later to see the posted grade and teacher feedback.
![output](/student.png "Snippet of Student Dashboard")

### A good first test

For a low-risk walkthrough, create a short assignment with two questions and one test student. Confirm that the student can open the document, type a small C++ program, compile it, save a draft, resume it, and submit. Then open the same attempt as the teacher and verify that the code, compiler output, timestamps, and activity events are visible before using the workflow for a graded assessment.



## Safety features at a glance
Codelab has the following security features.

### 1. Clipboard and transfer controls

- Blocks paste, copy, cut, drag-and-drop, context menus, and configured shortcut paths inside the assessment editor.
- Records blocked clipboard and transfer attempts instead of silently discarding them.
- Keeps the student in a dedicated Monaco tab for each configured question, so the expected answer surface is clear.

### 2. Focus and session integrity

- Starts a protected, fullscreen-oriented attempt with an explicit leave decision when fullscreen is exited.
- Records focus loss, hidden browser tabs, tab switches, reconnects, fullscreen exits, and session start/resume/save/submit events.
- Keeps server timestamps for the activity timeline and deadline decisions.

### 3. Runtime tamper evidence

- Hash-checks selected guard functions, including the logic responsible for clipboard and typing restrictions.
- Watches obvious changes to event registration, network transport, function stringification, and browser primitives.
- Emits a tamper event when a protected function or runtime guard changes; it does not pretend that client-side JavaScript can be made magically untouchable.

### 4. Signed heartbeats and chained telemetry

- The server issues short-lived, signed challenges for assessment-session calls.
- Heartbeats are sequenced and telemetry windows are hash-chained so missing, reordered, replayed, or altered activity is detectable.
- Invalid or missing heartbeat sequences can flag or expire an attempt according to server rules.

### 5. Keystroke-to-editor reconciliation

Codelab compares trusted browser input signals with the characters and edits reported by Monaco. Review signals include:

- trusted and untrusted `keydown` and `beforeinput` events;
- inserted and deleted character counts;
- inter-key timing and key-hold summaries;
- corrections, navigation, modifiers, and repeated keys;
- largest insertion, unexplained multi-character changes, burst speed, and change count.

Cadence, holds, bursts, and correction patterns can raise a review-oriented typing-risk indicator. Irregular pauses alone are not treated as proof that typing was human, and normal editor behavior such as auto-pairing or composition input requires tolerance.

### 6. Backend authority and data boundaries

- Supabase Row Level Security and server-side functions enforce role, ownership, enrollment, class membership, question membership, deadline, score, and source-size rules.
- Assignment documents remain in private storage and are served through authorized access paths.
- Grades, per-question marks, source snapshots, autosaves, and activity records remain tied to the correct attempt.
- Deadline submission is enforced through the supported client/database workflow rather than relying on a teacher to remember every open tab.

### 7. Safer compilation

The browser does not receive compiler secrets. C/C++ Compile & Run goes through the `compile-code` Edge Function to a configured Judge0-compatible service. Production deployments should isolate compilation with CPU, memory, process, output, network, and wall-clock limits, plus rate limiting and queue monitoring.

![output](/teacher-marking.png "Snippet of Teacher Marking a Pre-compiled Assignment Along with all Secuity Details")
## What teachers can review

The review screen brings the evidence together without turning a risk score into an automatic verdict:

- the uploaded brief, student source, compiler output, and standard input;
- start, resume, autosave, compile, and submission timestamps;
- paste/transfer attempts, focus changes, fullscreen exits, suspicious shortcuts, and tamper events;
- total keystrokes, average and peak WPM, unexplained edits, untrusted-input counts, and typing-risk indicators;
- per-question marks, confirmed grade overrides, and the final assignment grade.

**Evidence supports a decision; it does not replace one.** Teachers should consider accessibility needs, network interruptions, normal editor behavior, and institutional procedures before taking action.

## The technology behind the workflow

The interface uses React, Vite, React Router, and Monaco Editor. Supabase provides Auth, PostgreSQL, Row Level Security, Storage, RPC functions, and Edge Functions. PDF.js and Mammoth render assignment documents, while a Judge0-compatible service handles C17/C++17 compilation. PostgreSQL `pg_cron` supports deadline automation.

## A practical security boundary

Codelab is **tamper-evident**, not tamper-proof. A student controls their own device and browser, so determined automation, a second device, or operating-system-level tooling cannot be ruled out by web code alone. The design goal is to make direct copying harder, make suspicious changes visible, protect server decisions, and give teachers enough context for a fair review.

For stronger control, pair the platform with institution-managed devices, a secure browser, clear assessment policy, accessibility accommodations, and a documented review process.

## Tech Stack
**React + Vite** — *frontend application*\
**Monaco Editor** — *in-browser coding editor*\
**Supabase** — *authentication, PostgreSQL database, storage, security policies, and Edge Functions*\
**Judge0** — *C/C++ code compilation and execution*\
**PDF.js + Mammoth** — *PDF and DOCX assignment viewing*\
**PostgreSQL** — *data management and deadline automation*
