Address the findings in __REVIEW_FILE__.

Plan the fixes first, then apply them. Fix every "blocking" and every "medium"
finding; leave "low" alone. Set "resolved":true on each finding you address.

You may not downgrade a severity the reviewer assigned. If you disagree, say so
in that finding's summary and leave the severity as it is.

Commit the fixes. Never touch anything under .github/ or .opencode/.
