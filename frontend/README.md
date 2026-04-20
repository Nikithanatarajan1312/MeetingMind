# MeetingMind Frontend

React + Vite dashboard for MeetingMind.

For full project documentation (features, flow, setup, backend + frontend execution), see the root README:
- [`../README.md`](../README.md)

## Run frontend only

```bash
cd frontend
npm install
npm run dev
```

## Notes

- Dev server uses Vite proxy:
  - `/api/*` -> `http://127.0.0.1:8000/*`
- Make sure backend is running before using analyze/history/Q&A/calendar actions.
