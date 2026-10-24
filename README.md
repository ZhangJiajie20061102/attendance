# attendance

Simple Attendance by Seat Id — a small Java web application (no Spring, no
database) built with Ant/NetBeans, packaged as `attendance.war`, and deployed
to Render.com inside a Tomcat 9 Docker container.

## What's in this repo

| File | Purpose |
|------|---------|
| `attendance.war` | The web application (index.html + `attendance.Attend` servlet), built by the NetBeans Ant project in `source/` |
| `Dockerfile` | Tomcat 9 (JDK 11) image that runs the war |
| `entrypoint.sh` | Patches Tomcat's `server.xml` so Tomcat listens on Render's `$PORT` |

## How the servlet works

`Attend.java` keeps a static `HashMap<String,String> sid2seat` in memory and
handles three cases based on the `studentId` parameter:

- `studentId == null` — dump the map (Attendee List page)
- `studentId` not blank — record `(studentId, seatId)` and confirm
- `studentId` blank — show "Student Id can not be blank"

## Build (local, NetBeans Ant project)

The `source/` folder is the actual NetBeans **Java Web > Web Application**
project (`File > New Project`), including `build.xml` and the `nbproject/`
folder that NetBeans generates. Open `source/` in NetBeans and press F11
(Build), or run Ant from the command line:

```
cd source
ant clean dist          # NetBeans Ant build -> build/web + dist/attendance.war
jar -cvf attendance.war -C build/web .
```

`build/web` is the exploded web root; the `attendance.war` shipped in this
repo was produced by exactly that build (Java 11 bytecode, major version 55).

## Deploy (Render.com)

1. Push this repo to GitHub.
2. On https://dashboard.render.com click **New + > Web Service**, pick this repo.
3. Name: `attend95712`, Runtime: **Docker** (auto-detected), Branch: `main`,
   Instance Type: **Free**.
4. Create Web Service and wait for "Live".

App URLs:

- https://attend95712.onrender.com/ (the same war deployed as the root context)
- https://attend95712.onrender.com/attendance/ (the app under its own context)

## Notes

- Attendance records live in memory only: restarting the service or the
  container clears them (intentional simplification for the lab).
- On the free plan the service sleeps after ~15 minutes idle; the first
  request after sleeping takes some extra seconds to wake it up.
