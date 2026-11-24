# Requirements Document

## Introduction

A single-file offline web app (`index.html`) for a gas station cashier in Peru. The app tracks cash received, Yape/Culqi digital payments, and vault deposits during a shift — giving the cashier a real-time view of how much physical cash should be in the drawer. A floating action button opens a change calculator (vuelto) so the cashier can quickly compute change without leaving the main view. The interface is dark-themed, mobile-first, and built entirely with vanilla HTML, CSS, and JavaScript — no external dependencies.

## Glossary

- **App**: The single-file `index.html` web application described in this document.
- **Cashier**: The gas station employee operating the App.
- **Efectivo**: Physical cash received from customers, denominated in Peruvian Soles (S/).
- **Yape/Culqi**: Digital payment methods processed through a POS terminal; these do not add to the physical cash in the drawer.
- **Cierre F3**: A POS settlement cycle triggered per Yape/Culqi transaction; the App tracks the expected count.
- **Bóveda**: The secure vault where the Cashier deposits excess cash from the drawer.
- **Depósito**: A cash deposit made by the Cashier into the Bóveda, which reduces the Efectivo balance.
- **Vuelto**: Change returned to the customer in cash.
- **Efectivo en Caja**: The running total of physical cash currently expected in the drawer (Efectivo received minus Depósitos).
- **Total Digital**: The running total of all Yape/Culqi payments received.
- **Total Depositado**: The cumulative amount deposited into the Bóveda during the current session.
- **Total Vendido**: The sum of Efectivo, Total Digital, and Total Depositado.
- **FAB**: Floating Action Button — a circular button fixed to the bottom-right corner of the screen.
- **Session**: A single uninterrupted usage period from app load until the browser tab is closed or refreshed.
- **Movement**: Any single recorded transaction (cash entry, digital payment, or vault deposit).
- **Tab**: One of the three main navigation sections: Efectivo, Yape/Culqi, or Bóveda.

---

## Requirements

### Requirement 1: Single-File Offline Architecture

**User Story:** As a Cashier, I want an app that works without internet access, so that I can use it even when the station's connection is down.

#### Acceptance Criteria

1. THE App SHALL be delivered as a single `index.html` file containing all HTML, CSS, and JavaScript inline.
2. THE App SHALL function fully without any network requests, CDN links, or external dependencies.
3. THE App SHALL load and operate correctly in a modern mobile browser (Chrome for Android, Safari for iOS) without installation.

---

### Requirement 2: Default Tab on Load

**User Story:** As a Cashier, I want the app to open on the Efectivo tab, so that I can start registering cash immediately.

#### Acceptance Criteria

1. WHEN the App loads, THE App SHALL display the Efectivo tab as the active view.

---

### Requirement 3: Fixed Header with Efectivo en Caja

**User Story:** As a Cashier, I want to always see how much cash I should have in the drawer, so that I can verify my drawer at any moment.

#### Acceptance Criteria

1. THE App SHALL display a fixed header visible on all Tabs and on the Vuelto calculator view.
2. THE Header SHALL display the current Efectivo en Caja value in a font size of at least 32px.
3. WHEN Efectivo en Caja changes, THE Header SHALL update the displayed value within one rendering frame.
4. THE Header SHALL contain a toggle control that expands a summary panel showing Total Digital, Total Depositado, and Total Vendido.
5. WHEN the toggle control is activated, THE Header SHALL expand to show the summary panel.
6. WHEN the toggle control is activated again, THE Header SHALL collapse the summary panel.

---

### Requirement 4: Bottom Navigation Bar

**User Story:** As a Cashier, I want a persistent bottom navigation bar, so that I can switch between sections with one tap.

#### Acceptance Criteria

1. THE App SHALL display a fixed bottom navigation bar visible on all Tab views.
2. THE Navigation_Bar SHALL contain exactly three tab items: Efectivo, Yape/Culqi, and Bóveda.
3. WHEN a tab item is tapped, THE App SHALL display the corresponding Tab content.
4. THE Navigation_Bar SHALL visually indicate the currently active Tab.

---

### Requirement 5: Quick Cash Entry Buttons

**User Story:** As a Cashier, I want large one-tap buttons for common cash amounts, so that I can register payments without typing.

#### Acceptance Criteria

1. THE Efectivo_Tab SHALL display five quick-entry buttons with labeled amounts: S/5, S/10, S/15, S/20, and S/50.
2. WHEN a quick-entry button is tapped, THE App SHALL add the button's amount to Efectivo en Caja and record it as a Movement.
3. THE quick-entry buttons SHALL each have a minimum tap target height of 56px.

---

### Requirement 6: Variable Cash Entry

**User Story:** As a Cashier, I want to type a custom amount and add it, so that I can register sales that don't match the preset buttons.

#### Acceptance Criteria

1. THE Efectivo_Tab SHALL display a numeric input field with `type="text"`, `inputmode="decimal"`, and `pattern="[0-9]*[.,]?[0-9]*"`.
2. THE Efectivo_Tab SHALL display a "Sumar" button adjacent to the numeric input field.
3. WHEN the "Sumar" button is tapped and the numeric input contains a valid positive number, THE App SHALL add the parsed amount to Efectivo en Caja and record it as a Movement.
4. WHEN the "Sumar" button is tapped and the numeric input contains a comma as decimal separator, THE App SHALL parse the value correctly as a decimal number.
5. WHEN the "Sumar" button is tapped and the numeric input is empty or contains a non-positive value, THE App SHALL not modify Efectivo en Caja.
6. AFTER a successful entry via the "Sumar" button, THE App SHALL clear the numeric input field.

---

### Requirement 7: Undo Last Movement

**User Story:** As a Cashier, I want to undo my last action, so that I can correct a mistake without resetting everything.

#### Acceptance Criteria

1. THE Efectivo_Tab SHALL display a "Deshacer" button.
2. WHEN the "Deshacer" button is tapped and at least one Movement exists in the Session, THE App SHALL reverse the effect of the most recent Movement and remove it from the Movement history.
3. WHEN the "Deshacer" button is tapped and no Movements exist in the Session, THE App SHALL take no action.
4. THE "Deshacer" button SHALL undo only the single most recent Movement per tap.

---

### Requirement 8: Yape/Culqi Payment Registration

**User Story:** As a Cashier, I want to register digital payments, so that I can track the total amount paid via Yape or Culqi separately from cash.

#### Acceptance Criteria

1. THE Yape_Culqi_Tab SHALL display a numeric input field with `type="text"`, `inputmode="decimal"`, and `pattern="[0-9]*[.,]?[0-9]*"` for entering a digital payment amount.
2. THE Yape_Culqi_Tab SHALL display a confirmation button to submit the digital payment.
3. WHEN the confirmation button is tapped and the input contains a valid positive number, THE App SHALL add the parsed amount to Total Digital and record it as a Movement.
4. WHEN a digital payment Movement is recorded, THE App SHALL increment the expected Cierre F3 count by one.
5. WHEN a digital payment Movement is recorded, THE App SHALL NOT add the amount to Efectivo en Caja.
6. AFTER a successful digital payment entry, THE App SHALL clear the numeric input field.
7. THE Yape_Culqi_Tab SHALL display the current Total Digital and the current expected Cierre F3 count.

---

### Requirement 9: Vault Deposit Registration

**User Story:** As a Cashier, I want to record when I deposit cash into the vault, so that my Efectivo en Caja reflects what actually remains in the drawer.

#### Acceptance Criteria

1. THE Boveda_Tab SHALL display a numeric input field with `type="text"`, `inputmode="decimal"`, and `pattern="[0-9]*[.,]?[0-9]*"` for entering a deposit amount.
2. THE Boveda_Tab SHALL display a confirmation button to submit the deposit.
3. WHEN the confirmation button is tapped and the input contains a valid positive number, THE App SHALL subtract the parsed amount from Efectivo en Caja and add it to Total Depositado, recording it as a Movement.
4. WHEN the confirmation button is tapped and the input is empty or contains a non-positive value, THE App SHALL not modify any totals.
5. AFTER a successful deposit entry, THE App SHALL clear the numeric input field.
6. THE Boveda_Tab SHALL display the current Total Depositado.

---

### Requirement 10: Floating Action Button (FAB)

**User Story:** As a Cashier, I want a floating button to open the change calculator, so that I can compute change without navigating away from my current view.

#### Acceptance Criteria

1. THE App SHALL display a FAB fixed to the bottom-right corner of the screen, above the Navigation_Bar.
2. THE FAB SHALL have a minimum size of 64×64px.
3. WHEN the Vuelto calculator is not open, THE FAB SHALL display an orange background with the label "🔁 VUELTO".
4. WHEN the Vuelto calculator is open, THE FAB SHALL display a green background with the label "↩ VOLVER".
5. WHEN the FAB is tapped and the Vuelto calculator is not open, THE App SHALL display the Vuelto calculator view.
6. WHEN the FAB is tapped and the Vuelto calculator is open, THE App SHALL close the Vuelto calculator view and restore the previously active Tab.

---

### Requirement 11: Change Calculator (Calculador de Vueltos)

**User Story:** As a Cashier, I want to calculate change quickly, so that I can tell a customer their change and which bills to use without mental arithmetic.

#### Acceptance Criteria

1. THE Vuelto_Calculator SHALL display a numeric input field labeled "Consumo" with `type="text"`, `inputmode="decimal"`, and `pattern="[0-9]*[.,]?[0-9]*"`.
2. THE Vuelto_Calculator SHALL display three quick-select buttons for bill amounts received: S/20, S/50, and S/100.
3. THE Vuelto_Calculator SHALL display a numeric input field for manually entering the bill amount received by the Cashier.
4. WHEN a quick-select bill button is tapped, THE Vuelto_Calculator SHALL populate the received-amount field with the button's value and recalculate the Vuelto.
5. WHEN both the Consumo field and the received-amount field contain valid positive numbers and the received amount is greater than or equal to the Consumo amount, THE Vuelto_Calculator SHALL display the Vuelto in a font size of at least 48px.
6. WHEN the received amount is less than the Consumo amount, THE Vuelto_Calculator SHALL display a visual indicator that the received amount is insufficient, without showing a negative Vuelto.
7. WHEN the Vuelto is a positive value, THE Vuelto_Calculator SHALL display a suggested bill and coin breakdown that sums exactly to the Vuelto using the largest available denominations first (S/50, S/20, S/10, S/5, S/2, S/1, S/0.50, S/0.20, S/0.10).
8. THE Vuelto_Calculator SHALL NOT add any amount to Efectivo en Caja or any other running total.

---

### Requirement 12: Input Parsing for Decimal Separators

**User Story:** As a Cashier, I want to enter amounts using either a period or a comma as the decimal separator, so that I don't have to worry about keyboard locale.

#### Acceptance Criteria

1. WHEN any numeric input in the App receives a value containing a comma as the decimal separator, THE App SHALL parse the value as if the comma were a decimal point before performing any arithmetic.
2. WHEN any numeric input receives a value containing more than one comma or more than one period, THE App SHALL treat the input as invalid and not modify any totals.

---

### Requirement 13: Visual Design and Accessibility

**User Story:** As a Cashier, I want a high-contrast dark interface with large touch targets, so that I can operate the app quickly in outdoor or low-light conditions.

#### Acceptance Criteria

1. THE App SHALL apply a dark color theme with a background color luminance below 20% and foreground text luminance above 70%.
2. THE App SHALL render all primary action buttons with a minimum height of 56px.
3. THE App SHALL use a single-column layout with no horizontal scrolling required on viewports 320px wide or wider.
4. THE App SHALL NOT require vertical scrolling within any Tab to access all primary controls on a viewport at least 600px tall.
5. THE App SHALL apply distinct visual styling (color, border, or weight) to differentiate the active Tab in the Navigation_Bar.
