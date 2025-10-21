# Prominence II (Fabric 1.20.1) on Railway — ZIP mode (no CurseForge API key)

This repo lets you deploy **Prominence II: Hasturian Era** on **Railway** using the official `itzg/minecraft-server` image by supplying a **ZIP of the server pack**, either:
- a **local file** (baked into the image), or
- a **direct download URL** to the ZIP.

No CurseForge API key is required with this method.

---

## 1) What you need
- Railway account (connect with GitHub)
- The **Server Pack ZIP** for Prominence II (download it from the CurseForge Files page)
- A paid Railway plan is recommended (6–8 GB RAM, persistent volume, 24/7 uptime)

---

## 2) Files in this repo
- `Dockerfile` — minimal; the image handles the install
- `.env.example` — variables for ZIP mode (`TYPE=CURSEFORGE`, `CF_SERVER_MOD=...`)
- `.gitignore`

Optional (not included): the actual `prominence2-serverpack.zip` (place next to Dockerfile if you use local file mode).

---

## 3) Choose your ZIP source

### Option A: Local ZIP (recommended for deterministic builds)
1. Download the **Server Pack** for Prominence II from CurseForge.
2. Rename it to `prominence2-serverpack.zip` (or edit the name in `.env.example`).
3. Place the ZIP **next to** the `Dockerfile` (repo root).  
4. Ensure `.env` has:
   ```env
   TYPE=CURSEFORGE
   CF_SERVER_MOD=prominence2-serverpack.zip
   ```

### Option B: Direct URL
Use a direct link to the ZIP file (must end in `.zip` and be directly downloadable):
```env
TYPE=CURSEFORGE
CF_SERVER_MOD=https://example.com/path/to/prominence2-serverpack.zip
```

---

## 4) Deploy on Railway

1. Push this repo to GitHub (include the ZIP if you use Option A).
2. In Railway → **New Project → Deploy from GitHub** and select your repo.
3. In your service → **Variables**, add vars from `.env.example` (at minimum: `EULA=TRUE`, `TYPE=CURSEFORGE`, `CF_SERVER_MOD=...`, `MEMORY=6G`).  
4. **Persist data**: create a **Volume** and **mount it at `/data`** (critical for world/config persistence).
5. **Networking**: enable a **TCP Proxy** targeting **port 25565**. Railway will give you a host:port like:
   ```
   your-name.tcp.railway.app:12345
   ```
   Use that in the Minecraft client.

---

## 5) First boot
- The image accepts EULA, extracts the ZIP, installs loader/mods, and generates the world in `/data`.
- First boot can take a while — check **Logs** in Railway.

---

## 6) Server tuning (optional)
- `MEMORY=6G` (or 8G for heavier loads)
- `VIEW_DISTANCE=8`, `SIMULATION_DISTANCE=6–8`
- `USE_AIKAR_FLAGS=true` (or `USE_MEOWICE_FLAGS=true`)
- `OPS=YourNickHere`

---

## 7) Updating the pack
- Replace the local ZIP (Option A) or update the `CF_SERVER_MOD` URL (Option B), then redeploy/restart.
- World data stays intact if `/data` is properly mounted.

---

## 8) Local run (optional)
Example with Docker (local ZIP):
```bash
docker run -d --name prominence2-zip   -p 25565:25565   -e EULA=TRUE   -e TYPE=CURSEFORGE   -e CF_SERVER_MOD=prominence2-serverpack.zip   -e MEMORY=6G   -v $(pwd)/data:/data   -v $(pwd)/prominence2-serverpack.zip:/downloads/prominence2-serverpack.zip   itzg/minecraft-server:java17
```
> Mounting the file into `/downloads` is another compatible approach if you prefer not to bake it into the image.

---

**Enjoy!** If you want, I can also add a `docker-compose.yml` for easy local testing with volumes/ports.
