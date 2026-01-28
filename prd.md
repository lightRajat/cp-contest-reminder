# Product Requirements Document (PRD)

- **Project Name:** Contest Reminder
- **Platform:** Android (Native Kotlin)
- **Minimum SDK:** Android 8.0 (API 26)

**Contest Reminder** is an android utility application for Competitive Programmers to ensure that they never miss a contest by providing persistent notifications for registration and robust "wake-up" alarms.

## 1. Overview

- The app aggregates contest details from APIs of different competitive programming platforms and notifies the user about upcoming contests.
  - **Codeforces:** [GET] [https://codeforces.com/api/contest.list](https://codeforces.com/api/contest.list)
  - **LeetCode:** [GET] [https://alfa-leetcode-api.onrender.com/contests/upcoming](https://alfa-leetcode-api.onrender.com/contests/upcoming)
    > **Scalability Note:** While the initial version (MVP) supports *Codeforces* and *LeetCode*, the architecture **must be designed to easily extend** to other platforms (e.g., AtCoder, CodeChef) in future without major refactoring.
- A couple days before the contest starts, the app sends a notification, asking the users to register.
- For all the registered contests, the app rings an alarm before the contest start.
- The app consists of 4 fundamental components:
  1. **Home Screen:** Shows upcoming contests, along with a menu to filter contest-types
  2. **Menu:** Contains platform-specific settings
  3. **Registration Notification:** Asks the user to register for the contest
  4. **Contest Start Alarm:** Rings before the contest start

## 2. Screens

### 2.1. Home Screen

#### Header

* **Title:** `Upcoming Contests`
* **Action Icon:** Three-dots menu icon (Overflow Menu) on the top right.

#### List Items (Cards)

The page contains a list of cards for each upcoming contests, containing the following details.

* **Platform Name:** Small, Gray, Uppercase
* **Contest Name:** Large, Bold
* **Date/Time:** a small icon followed by date (Format: `Fri, 16 Oct at 2:35 PM`).
* **Status Badges:**
  * **NEW:** Blue-Colored
  * **REGISTERED:** Green (indicates alarm is active).
* **Visual Indicator:** Vertical-left/horizontal-top color stripe corresponding to each platform.
  * **CodeForces:** A gradient of yellow-blue-red
  * **LeetCode:** A gradient of gray-yellow

#### States

* *Loading:* Centered spinner with text `Fetching Contests`.
* *Offline:* Shows cached results along with a toast message `No internet. Swipe down to refresh.`
* *No contests:* "No contests found."

#### Interaction

* **Swipe Down:** Brings a spinner down, refreshes data and hides the spinner.
* **Card Click:** Opens the specific contest page in the default browser.

### 2.2. Menu (indicated by 3-dots icon)

#### Menu Items

* When the 3-dots icon is clicked, a dropdown menu appears showing a list of platforms, indicating platform-specific settings
> Right now, only *CodeForces* is the only option in this menu

#### Codeforces Filtering Logic

* Clicking "Codeforces" opens a **Popup Panel**.
* **Heading of popup panel:** `Filter Contests`
* **Content:** 5 Checkboxes for contest types:
  - [ ] Div. 1
  - [ ] Div. 2
  - [ ] Div. 3
  - [ ] Div. 4
  - [ ] Others (Educational, Global, etc.)

> These filters should be respected throughout the application.

### 2.3 Contest Start Alarm

#### Behavior

* **Screen OFF:** Wakes device, shows on top of lock screen, plays audio.
* **Screen ON:** Shows a high-priority ringing notification, similar to default alarm that shows up when the screen is on.

#### Design

* **Translucent Background:** Blurred background showing lock screen wallpaper.
* **Contest Details:**
  * **Heading:** `Contest Starting Soon` (top)
  * **Main Text:** `{Contest Name}` (Platform name as prefix) (center)
  * **Sub-Text:** `at {time}` if same day; `at {time} tomorrow` if the next day (center)
* **Dismissal:** 2 arrows at bottom radiating upwards, showing text `Swipe up to Dismiss`
* **Timeout:** Alarm auto stops after the full ringtone is played and goes into notif center

## 3. Background Activity

### 3.1. Registration Notification

* **Trigger:** Contest starts in < 48 hours.
* **Content:**
  * **Title:** `Upcoming Contest`
  * **Body:** `{Contest Name}` (Make sure that platform name appears as a prefix)

* **Action Button:** `Register`. Clicking this **must**:
  1. Dismiss the notification.
  2. Close the system notification shade (Status Bar).
  3. Open the contest URL in the browser.
  4. Mark the contest as "Registered" locally and not show reminder for this contest again.

### 3.2. The Alarm

* **Trigger:** Only for contests marked as "Registered".
* **Timing Rules:**
  * **Start time >= 12:00 PM:** Alarm rings **2 hours before**.
  * **Start time < 12:00 PM:** Alarm rings at **10:45 PM the previous night**.

## 4. Usability Constraints

* **Offline Mode:** App must operate on cached data if the internet is unavailable.
* **Reboot Safe:** Notifications and Alarms shouldn't be affected by reboots.
* **Doze-mode Safe:** App shouldn't be affected by any power saving mode, background process killing, and should work reliably like the default alarm app.
