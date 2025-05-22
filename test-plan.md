# Test Plan: lev4e dApp New Features

**Objective:** To verify the correct functionality and user experience of the newly implemented features in `index.html`, including Transaction Status Display, User Token Balance Display, and Client-Side Claim Streak.

**Tester:** Human

**Environment:** Web browser with MetaMask extension installed and configured for the relevant test network (e.g., Sepolia). Ensure the 5LV token contract is deployed and its address in `index.html` is correct.

**Prerequisites:**
*   Browser console should be open to monitor for errors.
*   Ability to clear `localStorage` for the dApp domain.
*   Ability to interact with MetaMask (connect, sign transactions, reject transactions).
*   (Optional, for advanced streak testing) Ability to modify system clock or `localStorage` values.

---

## 1. Transaction Status Display Tests

**Test Case 1.1: Connecting to MetaMask - Initial Connection**
*   **Steps:**
    1.  Open `index.html` in the browser.
    2.  Click the "dai 5lv" button.
    3.  Observe the status message area.
*   **Expected Outcome:**
    *   Status message "Connecting to MetaMask..." appears in the default (slate) color.
    *   "Thinking" image appears.
    *   Mouse cursor changes to "wait".
    *   MetaMask prompt for connection appears.

**Test Case 1.2: Connecting to MetaMask - User Rejects Connection**
*   **Steps:**
    1.  Follow steps for Test Case 1.1.
    2.  In the MetaMask prompt, click "Cancel" or "Reject".
    3.  Observe the status message area.
*   **Expected Outcome:**
    *   Status message "MetaMask connection rejected. Please allow connection to proceed." (or similar) appears in red.
    *   This error message should persist (not auto-clear immediately).
    *   "Thinking" image disappears.
    *   Mouse cursor returns to "default".

**Test Case 1.3: Requesting Signature for Claim**
*   **Steps:**
    1.  Successfully connect to MetaMask (accept connection if prompted).
    2.  If not already displayed, the dApp might proceed to the next step automatically, or click "dai 5lv" again if needed.
    3.  Observe the status message area before MetaMask prompts for transaction signature.
*   **Expected Outcome:**
    *   Status message "Requesting signature to claim 5LV..." appears in the default (slate) color.
    *   "Thinking" image is visible.
    *   Mouse cursor is "wait".
    *   MetaMask prompt for transaction signature appears.

**Test Case 1.4: User Rejects Signature**
*   **Steps:**
    1.  Follow steps for Test Case 1.3.
    2.  In the MetaMask transaction signature prompt, click "Reject".
    3.  Observe the status message area.
*   **Expected Outcome:**
    *   Status message "Transaction rejected. You must approve the transaction in MetaMask." (or similar, e.g., based on `err.reason`) appears in red.
    *   This error message should persist.
    *   "Thinking" image disappears.
    *   Mouse cursor returns to "default".

**Test Case 1.5: Transaction Submitted, Awaiting Confirmation**
*   **Steps:**
    1.  Follow steps for Test Case 1.3.
    2.  In the MetaMask transaction signature prompt, click "Confirm".
    3.  Observe the status message area immediately after confirming.
*   **Expected Outcome:**
    *   Status message "Transaction submitted. Awaiting confirmation..." appears in the default (slate) color.
    *   This message should persist until the transaction succeeds or fails.
    *   "Thinking" image is visible.
    *   Mouse cursor is "wait".

**Test Case 1.6: Transaction Successful**
*   **Steps:**
    1.  Follow steps for Test Case 1.5.
    2.  Wait for the transaction to be confirmed on the network.
    3.  Observe the status message area.
*   **Expected Outcome:**
    *   Status message "Transaction successful! 5LV tokens claimed." appears in green.
    *   This message should auto-clear after approximately 7 seconds.
    *   "Thinking" image disappears after the message appears (or shortly before).
    *   Mouse cursor returns to "default".
    *   Falling notes animation triggers.

**Test Case 1.7: Transaction Failure (Generic Contract Error)**
*   **Steps:**
    1.  Follow steps for Test Case 1.5.
    2.  (If possible to simulate) Ensure conditions for a contract revert (e.g., if the contract has a specific condition that can be triggered to cause a failure that isn't related to gas). This might be hard to simulate without modifying the contract or having a specific test function.
    3.  Alternatively, if a transaction fails for any other reason (e.g., network issue during `tx.wait()`, though less common for simple claims).
    4.  Observe the status message area.
*   **Expected Outcome:**
    *   A relevant error message (e.g., "Transaction failed: [reason from contract]" or "Transaction failed. Please try again.") appears in red.
    *   This error message should persist.
    *   "Thinking" image disappears.
    *   Mouse cursor returns to "default".

**Test Case 1.8: Status Message Styling and Clearing**
*   **Steps:**
    1.  Trigger various status messages as per previous test cases (error, success, info).
    2.  Observe their color and auto-clear behavior.
*   **Expected Outcome:**
    *   Error messages are red.
    *   Success messages are green.
    *   Informational messages are slate (default color).
    *   Success messages and temporary error messages (like "Could not fetch token balance") auto-clear after ~7 seconds.
    *   Persistent error messages (like user rejection, transaction failure) and "awaiting confirmation" messages do not auto-clear immediately.

**Test Case 1.9: "Thinking" Image and Wait Cursor Synchronization**
*   **Steps:**
    1.  Perform a full claim transaction from connection to success/failure.
    2.  Observe the "thinking" image and mouse cursor throughout the process.
*   **Expected Outcome:**
    *   "Thinking" image and "wait" cursor appear when an async operation starts (connecting, signing, submitting).
    *   They disappear when the operation concludes (success, failure, user rejection).

---

## 2. User Token Balance Display Tests

**Test Case 2.1: Initial State of Balance Display**
*   **Steps:**
    1.  Clear `localStorage` for the dApp.
    2.  Open `index.html`.
    3.  Observe the token balance display area before connecting MetaMask.
*   **Expected Outcome:**
    *   The `tokenBalanceContainer` is hidden, or if visible, the `userTokenBalance` span shows "--" or "0.00 5LV" (depending on if it's shown before connection). The requirement was `style="display: none;"` initially, so it should be hidden.

**Test Case 2.2: Balance Display After MetaMask Connection**
*   **Steps:**
    1.  Open `index.html`.
    2.  Click "dai 5lv" and successfully connect to MetaMask.
    3.  Observe the token balance display area.
*   **Expected Outcome:**
    *   The `tokenBalanceContainer` becomes visible.
    *   The `userTokenBalance` span displays the user's current 5LV token balance, formatted as "X.XX 5LV" (e.g., "123.00 5LV", "0.00 5LV").

**Test Case 2.3: Balance Update After Successful Claim**
*   **Steps:**
    1.  Follow Test Case 2.2 to connect and display initial balance.
    2.  Note the current balance.
    3.  Successfully perform a token claim (as in Test Case 1.6).
    4.  Observe the token balance display area after the claim is successful and the success message appears.
*   **Expected Outcome:**
    *   The `userTokenBalance` updates to reflect the newly claimed tokens. The value should increase by the claim amount.

**Test Case 2.4: Balance Fetch Failure (Simulated - Optional)**
*   **Steps:**
    1.  (Advanced) If possible, temporarily modify `tokenAddress` in the script to an invalid address or an address of a non-ERC20 contract *only for the balanceOf call* to simulate a fetch failure. This is difficult without code changes.
    2.  Alternatively, observe behavior if the RPC node is temporarily unavailable during the balance fetch (also hard to reliably test).
*   **Expected Outcome:**
    *   Status message "Could not fetch token balance." appears in red and auto-clears.
    *   The `userTokenBalance` might remain "--" or the last known value, or be hidden. The important part is the error status.

---

## 3. Client-Side Claim Streak Tests

**General Setup for Streak Tests:**
*   Start by clearing `localStorage` for the dApp domain to ensure a fresh state.
*   Connect wallet.

**Test Case 3.1: Initial Streak Display**
*   **Steps:**
    1.  Clear `localStorage`.
    2.  Open `index.html` and connect wallet.
    3.  Observe the claim streak container.
*   **Expected Outcome:**
    *   `claimStreakContainer` shows "Current Streak: <span id='currentStreak' class='font-bold'>0</span> days".

**Test Case 3.2: Scenario 1 - First Claim**
*   **Steps:**
    1.  Follow Test Case 3.1.
    2.  Perform a successful token claim.
    3.  Observe the streak display.
*   **Expected Outcome:**
    *   Streak updates to "Current Streak: <span class='font-bold'>1</span> days".
    *   `localStorage` should have `lastClaimDate` set to today's date and `currentClaimStreak` set to "1".

**Test Case 3.3: Scenario 2 - Claim on the Same Day**
*   **Steps:**
    1.  Follow Test Case 3.2.
    2.  Perform another successful token claim on the same day.
    3.  Observe the streak display.
*   **Expected Outcome:**
    *   Streak remains "Current Streak: <span class='font-bold'>1</span> days".
    *   `localStorage` values for `lastClaimDate` (today) and `currentClaimStreak` ("1") remain unchanged.

**Test Case 3.4: Scenario 3 - Claim on Consecutive Day**
*   **Steps:**
    1.  Follow Test Case 3.2 (achieve a 1-day streak).
    2.  **Simulate next day:**
        *   Option A (Manual): Change system clock to the next day.
        *   Option B (localStorage): Manually edit `localStorage`: set `lastClaimDate` to yesterday's date string (YYYY-MM-DD format).
    3.  Refresh `index.html` and reconnect wallet (if needed). The displayed streak should still be 1 (or 0 if the logic strictly breaks it on load, but the `updateClaimStreak(false)` should show 1 if yesterday was the last claim).
    4.  Perform a successful token claim.
    5.  Observe the streak display.
*   **Expected Outcome:**
    *   Streak increments to "Current Streak: <span class='font-bold'>2</span> days".
    *   `localStorage` has `lastClaimDate` set to the new "today's" date and `currentClaimStreak` set to "2".

**Test Case 3.5: Scenario 4 - Claim After Missing a Day**
*   **Steps:**
    1.  Achieve a streak (e.g., 1 or 2 days).
    2.  **Simulate missing a day (or more):**
        *   Option A (Manual): Change system clock forward by 2 days from the last claim date.
        *   Option B (localStorage): Manually edit `localStorage`: set `lastClaimDate` to a date string two (or more) days in the past.
    3.  Refresh `index.html` and reconnect wallet. The displayed streak should be 0.
    4.  Perform a successful token claim.
    5.  Observe the streak display.
*   **Expected Outcome:**
    *   Streak resets to "Current Streak: <span class='font-bold'>1</span> days".
    *   `localStorage` has `lastClaimDate` set to the new "today's" date and `currentClaimStreak` set to "1".

**Test Case 3.6: Scenario 5 - Streak Milestones (3-Day)**
*   **Steps:**
    1.  Achieve a 2-day streak (e.g., by following Test Case 3.4).
    2.  Simulate the next day and perform a successful claim.
*   **Expected Outcome:**
    *   Streak updates to "Current Streak: <span class='font-bold'>3</span> days".
    *   Status message "🎉 3-Day Streak! Keep it up! 🎉" appears in green and auto-clears.

**Test Case 3.7: Scenario 5 - Streak Milestones (7-Day)**
*   **Steps:**
    1.  Achieve a 6-day streak.
    2.  Simulate the next day and perform a successful claim.
*   **Expected Outcome:**
    *   Streak updates to "Current Streak: <span class='font-bold'>7</span> days".
    *   Status message "🔥🔥 7-Day Streak! You're on fire! 🔥🔥" appears in green and auto-clears.
    *   (Verify that the 3-day message does NOT appear if the logic uses `else if` and the 7-day message takes precedence. If separate `if`s are used, this is fine, but the 7-day is more significant). *Based on current code, separate `if`s are used, so this is fine.*

**Test Case 3.8: Streak Persistence**
*   **Steps:**
    1.  Achieve any streak (e.g., 2 days).
    2.  Note the `lastClaimDate` and `currentClaimStreak` in `localStorage`.
    3.  Close the browser tab/window.
    4.  Reopen the browser and navigate to `index.html`.
    5.  Connect wallet.
    6.  Observe the streak display.
*   **Expected Outcome:**
    *   The streak display should correctly reflect the state from `localStorage`. For example, if `lastClaimDate` was today, it shows the current streak. If `lastClaimDate` was yesterday, it shows the current streak. If `lastClaimDate` was older, it shows 0. This is handled by `updateClaimStreak(false)`.

---

## 4. Overall UI/UX Tests

**Test Case 4.1: UI Consistency**
*   **Steps:**
    1.  Visually inspect the new UI elements: Transaction Status `div`, Token Balance `div`, Claim Streak `div`.
*   **Expected Outcome:**
    *   Styling (font, colors, spacing, centering) is consistent with the existing page elements (title, button, footer).
    *   Elements are responsive and look good on typical desktop browser window sizes.

**Test Case 4.2: JavaScript Console Errors**
*   **Steps:**
    1.  Keep the browser developer console open throughout all testing.
    2.  Perform all above test cases.
*   **Expected Outcome:**
    *   No unexpected JavaScript errors appear in the console. Warnings (e.g., for non-critical issues like "token add rejected") are acceptable if understood.

**Test Case 4.3: General Responsiveness and Usability**
*   **Steps:**
    1.  Interact with the dApp as a typical user would.
    2.  Attempt claims, connect/disconnect wallet (if applicable through MetaMask, not dApp UI), observe flows.
*   **Expected Outcome:**
    *   The dApp feels responsive.
    *   Information flow is logical.
    *   No confusing or broken user interactions.

---

**End of Test Plan**
