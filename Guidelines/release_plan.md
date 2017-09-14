# Release Plan

The plan should be used when a release is being rolled out from version control all the way to production.

## Versioning

- For beta releases we append -RC followed by the release number for that beta. For example, 6.4.1-RC1 would be the first 
beta release, whilst 6.4.1-RC2 would be the beta release following on from that.

- Generally, for bug fix releases and small improvements we will increment the final digit in the version number. 
So for example, 6.4.1 would be incrememented to 6.4.2 and so on.

- For minor feature releases we simply increment the middle digit in the version number. So for example, 6.4.1 would be incrememented
to 6.5.0 and so on.

- For major releases we simply increment the first digit in the version number. So for example, 6.4.1 would be incrememented
to 7.0.0 and so on.

## Releasing a version

When releasing to beta, most features will be in beta for 2 - 5 days. The timeframe will depend on the size of the feature - 
but generally major releases will be in beta for 5 days and minor release will be based off of your own judgement 🙂

When releasing to production, we use the following timeframes:

- 10% at the first day of the release
- 25% on the following day
- 60% after the release has been stay 25% for 2 days
- 100% the following day

This means that if a release begins rollout on a Monday, it can hit 100% of our users by the end of the week. 
Having the release at 25% for two days also gives us a good amount of time at a decent sized userbase to catch any
oddities that may have slipped through testing and/or beta releases.
