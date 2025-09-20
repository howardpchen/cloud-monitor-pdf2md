# Deployment Assets

This directory contains systemd unit templates and environment scaffolding for
running Ink2MD as a managed service.

## Files

The systemd unit templates contain placeholders such as `${INSTALL_PREFIX}` and `${SERVICE_USER}`. The installer script `scripts/install_service.sh` renders them automatically; when installing by hand, replace the placeholders yourself before copying files into `/etc/systemd/system`.

- `systemd/ink2md.service` – main service definition. Adjust the
  user, working directory, and virtual environment path before installing under
  `/etc/systemd/system/`.
- `systemd/ink2md.env` – example environment file for credentials
  and API keys. Copy to `/etc/ink2md/env` with restricted permissions.
- `systemd/ink2md-healthcheck.service` / `.timer` – oneshot
  service and timer that run the repository's health-check script on an
  interval. Update the script path and thresholds as needed.
- `systemd/ink2md-purge.service` / `.timer` – oneshot service and
  timer that prune generated Markdown and attachments beyond the retention
  window.

## Installation Outline

```bash
sudo install -o root -g root -m 644 deploy/systemd/ink2md.service \
  /etc/systemd/system/ink2md.service
sudo install -o root -g root -m 640 deploy/systemd/ink2md.env \
  /etc/ink2md/env
sudo systemctl daemon-reload
sudo systemctl enable --now ink2md

# Optional timers
sudo install -o root -g root -m 644 deploy/systemd/ink2md-healthcheck.* \
  /etc/systemd/system/
sudo install -o root -g root -m 644 deploy/systemd/ink2md-purge.* \
  /etc/systemd/system/
sudo systemctl enable --now ink2md-healthcheck.timer
sudo systemctl enable --now ink2md-purge.timer
```

These templates assume the repository is checked out at `/opt/ink2md`
and that Python lives in `/opt/ink2md/.venv/bin`. Update the paths if you
choose a different layout.
