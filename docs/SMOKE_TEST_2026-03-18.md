# [2026-03-18] ShieldBox [test] Run smoke test

## Goal

Confirm the quote-request experience still renders correctly, preserves theme behavior, and completes the primary quote flow with no obvious regression.

## Pre Check

1. Confirm `index.html` is the current gold-master entry file
2. Confirm favicon assets resolve from `assets/icons/favicon/`
3. Confirm the quote calculator/configurator data is available and the page opens with no missing asset errors

## Core Checks

1. Open the app and confirm the first screen renders correctly
2. Toggle between the available visual themes if present
3. Fill one representative quote request from start to submit-ready state
4. Change scope/billing choices and confirm summary math/text updates correctly
5. Trigger one validation state and confirm feedback appears
6. Reload and confirm the app still boots cleanly
7. Confirm one edge feature touched by the patch: favicon paths and head metadata still resolve correctly

## UI Checks

1. Confirm focus order is usable by keyboard through the main form controls
2. Confirm the primary mobile layout does not break
3. Confirm status, validation, or confirmation feedback still appears

## Failure Log

- Area:
  Result:
  Notes:

## Exit Rule

Ship only when the quote flow and one adjacent theme/layout flow both pass.
