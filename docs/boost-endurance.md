# Boost-endurance: long rides and other low-step endurance exercise

This branch is `dev` with three changes to how Boost handles long aerobic efforts that produce few
steps, such as cycling, rowing or paddling. All three are live. Each can only reduce insulin. They
need heart-rate integration (Wear OS or Garmin) switched on, because steps alone cannot see a ride.

## Why

On `dev`, Boost decides what you are doing one cycle at a time. A ride produces almost no steps, so
without heart rate the whole ride reads as inactivity and the profile is raised to 130%, which adds
insulin. With heart rate, a steady effort in zone 3 or 4 reads as resistance exercise (target 160
mg/dL, profile unchanged), but easy riding in zone 2 and every descent or stop reads as rest, gets
the 130% raise, and ends the exercise bout, so a ride becomes a string of short bouts.

## What changes

The inactivity raise is withheld whenever heart rate is in zone 2 or above, that is at least 30% of
your heart-rate reserve. Nothing else changes at zone 2, because ordinary daily heart rate reaches
it too. This change is also in the standard build.

A new endurance state holds the whole effort together. After 30 minutes of heart rate in zone 2 or
above with fewer than 100 steps in each 15 minutes (gaps of up to 5 minutes allowed), Boost treats
you as doing endurance exercise: the profile goes to Endurance Profile Percent (60% by default) and
the target to 150 mg/dL (8.3 mmol/L), or 160 if heart rate alone has already raised it that far,
unless you have set a temporary target. It lasts until 20 minutes pass without qualifying heart
rate, so descents and café stops do not end it. V6 treats it as exercise, which holds off the
fast-carb confirm and the early primer.

The recovery window after exercise now grows with the length of the bout: the configured window
(2 h by default) times the bout length in hours, between 1 and 4 times, capped at 12 hours. A ride
of four hours or more therefore gets 8 hours of recovery at the defaults, with a target of 144 mg/dL
(8.0 mmol/L) and Boost's bolus cap and scale at 0.4. After an endurance bout the window applies even
when Post-exercise recovery is switched off; other exercise still follows that switch.

## Settings

Endurance exercise (cycling, rowing) switches the state on or off and is on by default. Endurance
Profile Percent sets the reduction, from 30% to 100%. Both are with the activity settings.

## Limits

This has not been tested on a recorded ride. A weights session with steady heart rate and little
walking can qualify after 30 minutes and receive the profile reduction, where guidance for
resistance work keeps the profile. A high temporary target still switches Boost off for its
duration, as on `dev`, and remains the simplest protection for a planned ride.
