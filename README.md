# Heroku Inspector Buildpack

This buildpack allows you to SSH into your Heroku build environment for interactive debugging.

## Usage

1. Push this buildpack to a Git repository (e.g., GitHub)

2. Add it to your Heroku app's buildpacks (should be FIRST in the chain):
   ```bash
   heroku buildpacks:add --index 1 https://github.com/YOUR_USERNAME/heroku-buildpack-inspector
   ```

3. When you need to debug a build, set the environment variable:
   ```bash
   heroku config:set INSPECT_BUILD=true
   ```

4. Trigger a build:
   ```bash
   git commit --allow-empty -m "trigger build for inspection"
   git push heroku main
   ```

5. Watch the build logs for the tmate SSH connection string:
   ```bash
   heroku logs --tail
   ```

6. SSH into the build environment using the provided connection string

7. When done inspecting, create the continue file:
   ```bash
   touch /tmp/continue_build
   ```
   Or just cancel the build with Ctrl+C locally

8. Don't forget to disable inspection when done:
   ```bash
   heroku config:unset INSPECT_BUILD
   ```

## How it works

- Uses `tmate` to create a reverse SSH session
- Pauses the build process until you create `/tmp/continue_build`
- Times out after Heroku's build timeout (~15 minutes)
- Only activates when `INSPECT_BUILD=true` is set

## Build Environment

You'll have access to:
- `$BUILD_DIR` - Your app's source code
- `$CACHE_DIR` - Buildpack cache
- All environment variables
- The build slug as it's being constructed
