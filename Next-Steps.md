`TODO: Page needs to be redone. Copied from end section of Hello World.`
`TODO: Should have short list of most used API to jump to, roadmap on how to use docs, option to do crash course`

## Further Reading

 - **[Glossary](https://gun.eco/docs/Glossary)**<br>
 Graph databases and distributed systems each have field specific terminology.  On top of those, gun has some of its own terminology. The glossary provides a concise list of terms you may not have seen before.

 - **[GUN’s Data Format](https://gun.eco/docs/GUN%E2%80%99s-Data-Format-(JSON))**<br>
 Everything you save into gun gets turned into a JSON subset. This page explains more of how and why.

 - **[Circular References](https://gun.eco/docs/Partials-and-Circular-References-(v0.2.x))**<br>
 As mentioned before, gun supports circular references between data. Read this if you wanna know how it works.

 - **[CAP Theorem](https://gun.eco/docs/CAP-Theorem)**<br>
 The CAP Theorem says you there's Consistency, Availability, and Partition Tolerance, and databases can only choose two. This page explains which gun chooses and why.

 - **[Conflict Resolution with Guns](https://gun.eco/docs/Conflict-Resolution-with-Guns)**<br>
 Since gun is offline-first in one of the harshest programming environments on earth (the browser), it's conflict resolution algorithm has to be rock-solid. This page gives a high-level overview.

## How To:
 - **[Start Your Own Gun Server](https://gun.eco/docs/How-to-Run-A-GUN-Server)**<br>
 In order to sync data between two peers, you'll need at least one gun server. This is how.

 - **[Delete Data](https://gun.eco/docs/Delete)**<br>
 Deleting stuff in distributed systems is harder than you'd think. Read this to see what nuances are at play and some tricks for dealing with them.

 - **[Use Amazon S3 for Storage](https://gun.eco/docs/Using-Amazon-S3-for-Storage)**<br>
 One of our storage plugins syncs directly with Amazon's S3 service, so you can reliably persist your data for mere pennies.

 - **[Build Modules for Gun](https://gun.eco/docs/Building-Modules-for-Gun)**<br>
 Although gun comes bundled with storage and transport plugins, you can swap them out for others. This page explains how to build your own.
