# Tracker
MY life
import { useState } from "react";

const ACCENT = "#7C6AF7";
const GREEN = "#4ECDC4";
const AMBER = "#F7A26A";
const RED = "#FF6B6B";

const MONTHS = ["Jan","Feb","Mar","Apr","May","Jun","Jul","Aug","Sep","Oct","Nov","Dec"];
const DAYS = ["Sun","Mon","Tue","Wed","Thu","Fri","Sat"];
const now = new Date();
const TODAY = DAYS[now.getDay()] + ", " + MONTHS[now.getMonth()] + " " + now.getDate();

const NAV = [
  { id: "dashboard", label: "Home", icon: "⊞" },
  { id: "applications", label: "Apply", icon: "📬" },
  { id: "tasks", label: "Tasks", icon: "✓" },
  { id: "goals", label: "Goals", icon: "◎" },
  { id: "stats", label: "Stats", icon: "▦" },
];

const STATUS_CONFIG = {
  wishlist:   { label: "Wishlist",   color: "#555" },
  applied:    { label: "Applied",    color: ACCENT },
  interview:  { label: "Interview",  color: AMBER },
  offer:      { label: "Offer",      color: GREEN },
  rejected:   { label: "Rejected",   color: RED },
};

function Card({ children, style, onClick }) {
  return (
    <div onClick={onClick} style={{
      background: "#13131A", border: "1px solid #1E1E2E",
      borderRadius: 14, padding: "18px 20px",
      cursor: onClick ? "pointer" : "default", ...style,
    }}>{children}</div>
  );
}

function Label({ children }) {
  return (
    <div style={{
      fontSize: 9, letterSpacing: "0.25em", textTransform: "uppercase",
      color: "#444", marginBottom: 12, fontFamily: "monospace",
    }}>{children}</div>
  );
}

function Pill({ color, children }) {
  return (
    <span style={{
      background: color + "22", color, borderRadius: 20,
      padding: "2px 10px", fontSize: 10, letterSpacing: "0.05em",
    }}>{children}</span>
  );
}

function ProgressBar({ pct, color }) {
  return (
    <div style={{ height: 4, background: "#1A1A28", borderRadius: 2, overflow: "hidden" }}>
      <div style={{
        height: "100%", width: Math.min(100, pct) + "%",
        background: color || ACCENT, borderRadius: 2, transition: "width 0.4s ease",
      }} />
    </div>
  );
}

function Input({ value, onChange, placeholder, type, style }) {
  return (
    <input value={value} onChange={onChange} placeholder={placeholder} type={type || "text"}
      style={{
        background: "#0C0C14", border: "1px solid #1E1E2E", color: "#D0C8E8",
        borderRadius: 8, padding: "8px 12px", fontSize: 13, outline: "none",
        fontFamily: "Georgia, serif", ...style,
      }} />
  );
}

function Btn({ onClick, children, color, small }) {
  return (
    <button onClick={onClick} style={{
      background: (color || ACCENT) + "22", border: "1px solid " + (color || ACCENT) + "55",
      color: color || ACCENT, borderRadius: 8,
      padding: small ? "5px 12px" : "8px 18px",
      cursor: "pointer", fontSize: small ? 11 : 13,
    }}>{children}</button>
  );
}

// ── APPLICATIONS ─────────────────────────────────────────────────────────────
function Applications({ apps, setApps }) {
  const [form, setForm] = useState({ company: "", role: "", status: "applied", date: "" });
  const [filter, setFilter] = useState("all");

  const add = () => {
    if (!form.company.trim() || !form.role.trim()) return;
    const d = new Date();
    setApps(prev => [...prev, {
      id: Date.now(), ...form,
      date: form.date || (MONTHS[d.getMonth()] + " " + d.getDate()),
      notes: "",
    }]);
    setForm({ company: "", role: "", status: "applied", date: "" });
  };

  const updateStatus = (id, status) => setApps(prev => prev.map(a => a.id === id ? { ...a, status } : a));
  const remove = (id) => setApps(prev => prev.filter(a => a.id !== id));

  const visible = filter === "all" ? apps : apps.filter(a => a.status === filter);

  return (
    <div>
      <Label>Add Application</Label>
      <Card style={{ marginBottom: 16 }}>
        <div style={{ display: "flex", gap: 8, flexWrap: "wrap", marginBottom: 10 }}>
          <Input value={form.company} onChange={e => setForm(p => ({ ...p, company: e.target.value }))} placeholder="Company" style={{ flex: 1, minWidth: 120 }} />
          <Input value={form.role} onChange={e => setForm(p => ({ ...p, role: e.target.value }))} placeholder="Role / Position" style={{ flex: 1, minWidth: 120 }} />
        </div>
        <div style={{ display: "flex", gap: 8, flexWrap: "wrap" }}>
          <select value={form.status} onChange={e => setForm(p => ({ ...p, status: e.target.value }))}
            style={{ background: "#0C0C14", border: "1px solid #1E1E2E", color: "#888", borderRadius: 8, padding: "8px 10px", fontSize: 12, outline: "none" }}>
            {Object.entries(STATUS_CONFIG).map(([k, v]) => <option key={k} value={k}>{v.label}</option>)}
          </select>
          <Btn onClick={add} color={GREEN}>+ Add</Btn>
        </div>
      </Card>

      <div style={{ display: "flex", gap: 6, flexWrap: "wrap", marginBottom: 14 }}>
        {["all", ...Object.keys(STATUS_CONFIG)].map(f => (
          <button key={f} onClick={() => setFilter(f)} style={{
            background: filter === f ? ACCENT + "22" : "transparent",
            border: "1px solid " + (filter === f ? ACCENT : "#222"),
            color: filter === f ? ACCENT : "#444", borderRadius: 20,
            padding: "4px 12px", fontSize: 10, cursor: "pointer", letterSpacing: "0.05em",
          }}>
            {f === "all" ? "All" : STATUS_CONFIG[f].label}
          </button>
        ))}
      </div>

      {visible.length === 0 && <div style={{ textAlign: "center", color: "#2A2A3A", padding: "40px 0", fontSize: 13 }}>No applications here yet</div>}

      {visible.map(app => (
        <Card key={app.id} style={{ marginBottom: 10 }}>
          <div style={{ display: "flex", justifyContent: "space-between", alignItems: "flex-start", gap: 8 }}>
            <div style={{ flex: 1 }}>
              <div style={{ fontSize: 14, color: "#D8D0F0", marginBottom: 2 }}>{app.company}</div>
              <div style={{ fontSize: 11, color: "#555", marginBottom: 8 }}>{app.role}</div>
              <div style={{ display: "flex", gap: 6, flexWrap: "wrap" }}>
                {Object.entries(STATUS_CONFIG).map(([k, v]) => (
                  <button key={k} onClick={() => updateStatus(app.id, k)} style={{
                    background: app.status === k ? v.color + "33" : "transparent",
                    border: "1px solid " + (app.status === k ? v.color : "#222"),
                    color: app.status === k ? v.color : "#333",
                    borderRadius: 20, padding: "3px 10px", fontSize: 9,
                    cursor: "pointer", transition: "all 0.2s",
                  }}>{v.label}</button>
                ))}
              </div>
            </div>
            <div style={{ display: "flex", flexDirection: "column", alignItems: "flex-end", gap: 6 }}>
              <span style={{ fontSize: 9, color: "#333", fontFamily: "monospace" }}>{app.date}</span>
              <span onClick={() => remove(app.id)} style={{ color: "#222", cursor: "pointer", fontSize: 18, lineHeight: 1 }}>×</span>
            </div>
          </div>
        </Card>
      ))}
    </div>
  );
}

// ── TASKS ────────────────────────────────────────────────────────────────────
function Tasks({ tasks, setTasks }) {
  const [input, setInput] = useState("");
  const [priority, setPriority] = useState("medium");
  const pColor = { high: RED, medium: AMBER, low: GREEN };

  const add = () => {
    if (!input.trim()) return;
    setTasks(prev => [...prev, { id: Date.now(), text: input.trim(), done: false, priority }]);
    setInput("");
  };

  const toggle = (id) => setTasks(prev => prev.map(t => t.id === id ? { ...t, done: !t.done } : t));
  const remove = (id) => setTasks(prev => prev.filter(t => t.id !== id));

  return (
    <div>
      <Label>Career Tasks</Label>
      <Card style={{ marginBottom: 16 }}>
        <div style={{ display: "flex", gap: 8, flexWrap: "wrap" }}>
          <Input value={input} onChange={e => setInput(e.target.value)}
            onKeyDown={e => e.key === "Enter" && add()} placeholder="Add a task..." style={{ flex: 1, minWidth: 160 }} />
          <select value={priority} onChange={e => setPriority(e.target.value)}
            style={{ background: "#0C0C14", border: "1px solid #1E1E2E", color: "#888", borderRadius: 8, padding: "8px 10px", fontSize: 12, outline: "none" }}>
            <option value="high">High</option>
            <option value="medium">Medium</option>
            <option value="low">Low</option>
          </select>
          <Btn onClick={add}>Add</Btn>
        </div>
      </Card>

      {tasks.length === 0 && <div style={{ textAlign: "center", color: "#2A2A3A", padding: "40px 0", fontSize: 13 }}>No tasks yet</div>}

      {["high", "medium", "low"].map(p => {
        const group = tasks.filter(t => t.priority === p);
        if (!group.length) return null;
        return (
          <div key={p} style={{ marginBottom: 16 }}>
            <div style={{ fontSize: 9, color: pColor[p], letterSpacing: "0.15em", textTransform: "uppercase", marginBottom: 8 }}>{p} priority</div>
            {group.map(task => (
              <Card key={task.id} style={{ marginBottom: 8, padding: "12px 16px" }}>
                <div style={{ display: "flex", alignItems: "center", gap: 12 }}>
                  <div onClick={() => toggle(task.id)} style={{
                    width: 18, height: 18, borderRadius: 4,
                    border: "1.5px solid " + (task.done ? pColor[p] : "#2E2E3A"),
                    background: task.done ? pColor[p] : "transparent",
                    cursor: "pointer", display: "flex", alignItems: "center", justifyContent: "center",
                    flexShrink: 0, transition: "all 0.2s",
                  }}>
                    {task.done && <svg width="10" height="8" viewBox="0 0 10 8" fill="none"><path d="M1 4L3.5 6.5L9 1" stroke="#fff" strokeWidth="1.8" strokeLinecap="round" strokeLinejoin="round"/></svg>}
                  </div>
                  <span style={{ flex: 1, fontSize: 13, color: task.done ? "#333" : "#B8B0D0", textDecoration: task.done ? "line-through" : "none", transition: "all 0.2s" }}>{task.text}</span>
                  <span onClick={() => remove(task.id)} style={{ color: "#222", cursor: "pointer", fontSize: 18, lineHeight: 1 }}>×</span>
                </div>
              </Card>
            ))}
          </div>
        );
      })}
    </div>
  );
}

// ── GOALS ────────────────────────────────────────────────────────────────────
function Goals({ goals, setGoals }) {
  const [form, setForm] = useState({ name: "", target: "", unit: "" });

  const add = () => {
    if (!form.name.trim() || !form.target) return;
    setGoals(prev => [...prev, { id: Date.now(), name: form.name.trim(), target: Number(form.target), current: 0, unit: form.unit || "%" }]);
    setForm({ name: "", target: "", unit: "" });
  };

  const adjust = (id, delta) => setGoals(prev => prev.map(g => {
    if (g.id !== id) return g;
    return { ...g, current: Math.min(g.target, Math.max(0, g.current + delta)) };
  }));

  const remove = (id) => setGoals(prev => prev.filter(g => g.id !== id));

  return (
    <div>
      <Label>Career Goals</Label>
      <Card style={{ marginBottom: 16 }}>
        <div style={{ display: "flex", gap: 8, flexWrap: "wrap" }}>
          <Input value={form.name} onChange={e => setForm(p => ({ ...p, name: e.target.value }))} placeholder="Goal name..." style={{ flex: 2, minWidth: 130 }} />
          <Input value={form.target} onChange={e => setForm(p => ({ ...p, target: e.target.value }))} placeholder="Target" type="number" style={{ width: 76 }} />
          <Input value={form.unit} onChange={e => setForm(p => ({ ...p, unit: e.target.value }))} placeholder="Unit" style={{ width: 70 }} />
          <Btn onClick={add} color={GREEN}>Add</Btn>
        </div>
      </Card>

      {goals.length === 0 && <div style={{ textAlign: "center", color: "#2A2A3A", padding: "40px 0", fontSize: 13 }}>No goals yet</div>}

      {goals.map(g => {
        const pct = Math.min(100, Math.round((g.current / g.target) * 100));
        return (
          <Card key={g.id} style={{ marginBottom: 12 }}>
            <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 8 }}>
              <span style={{ fontSize: 14, color: pct >= 100 ? GREEN : "#D0C8E8" }}>{g.name}</span>
              <div style={{ display: "flex", alignItems: "center", gap: 8 }}>
                <Pill color={pct >= 100 ? GREEN : ACCENT}>{pct}%</Pill>
                <span onClick={() => remove(g.id)} style={{ color: "#222", cursor: "pointer", fontSize: 18, lineHeight: 1 }}>×</span>
              </div>
            </div>
            <ProgressBar pct={pct} color={pct >= 100 ? GREEN : ACCENT} />
            <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginTop: 10 }}>
              <span style={{ fontSize: 11, color: "#444" }}>{g.current} / {g.target} {g.unit}</span>
              <div style={{ display: "flex", gap: 6 }}>
                <button onClick={() => adjust(g.id, -1)} style={{ background: "#1A1A28", border: "1px solid #2A2A3A", color: "#666", borderRadius: 6, width: 28, height: 28, cursor: "pointer", fontSize: 16 }}>−</button>
                <button onClick={() => adjust(g.id, 1)} style={{ background: ACCENT + "22", border: "1px solid " + ACCENT + "44", color: ACCENT, borderRadius: 6, width: 28, height: 28, cursor: "pointer", fontSize: 16 }}>+</button>
              </div>
            </div>
          </Card>
        );
      })}
    </div>
  );
}

// ── STATS ────────────────────────────────────────────────────────────────────
function Stats({ apps, tasks, goals }) {
  const total = apps.length;
  const byStatus = Object.keys(STATUS_CONFIG).reduce((acc, k) => {
    acc[k] = apps.filter(a => a.status === k).length;
    return acc;
  }, {});

  const responseRate = total > 0 ? Math.round(((byStatus.interview + byStatus.offer) / total) * 100) : 0;
  const offerRate = total > 0 ? Math.round((byStatus.offer / total) * 100) : 0;
  const tasksDone = tasks.filter(t => t.done).length;
  const taskPct = tasks.length > 0 ? Math.round((tasksDone / tasks.length) * 100) : 0;
  const avgGoal = goals.length > 0
    ? Math.round(goals.reduce((a, g) => a + Math.min(100, Math.round((g.current / g.target) * 100)), 0) / goals.length)
    : 0;

  const StatBox = ({ label, value, color, sub }) => (
    <Card style={{ flex: 1, minWidth: 110, textAlign: "center" }}>
      <div style={{ fontSize: 26, color: color || "#E0D8F0", fontWeight: "bold", marginBottom: 4 }}>{value}</div>
      <div style={{ fontSize: 9, color: "#444", letterSpacing: "0.12em", textTransform: "uppercase", marginBottom: 3 }}>{label}</div>
      {sub && <div style={{ fontSize: 10, color: "#333" }}>{sub}</div>}
    </Card>
  );

  return (
    <div>
      <Label>Overview</Label>
      <div style={{ display: "flex", gap: 10, flexWrap: "wrap", marginBottom: 20 }}>
        <StatBox label="Applied" value={total} color={ACCENT} />
        <StatBox label="Interviews" value={byStatus.interview} color={AMBER} />
        <StatBox label="Offers" value={byStatus.offer} color={GREEN} />
        <StatBox label="Rejected" value={byStatus.rejected} color={RED} />
      </div>

      <Label>Rates</Label>
      <Card style={{ marginBottom: 16 }}>
        {[
          { label: "Response Rate", pct: responseRate, color: AMBER },
          { label: "Offer Rate", pct: offerRate, color: GREEN },
          { label: "Tasks Complete", pct: taskPct, color: ACCENT },
          { label: "Avg Goal Progress", pct: avgGoal, color: "#A78BFA" },
        ].map(row => (
          <div key={row.label} style={{ marginBottom: 14 }}>
            <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 5 }}>
              <span style={{ fontSize: 12, color: "#777" }}>{row.label}</span>
              <span style={{ fontSize: 11, color: row.color, fontFamily: "monospace" }}>{row.pct}%</span>
            </div>
            <ProgressBar pct={row.pct} color={row.color} />
          </div>
        ))}
      </Card>

      <Label>Application Pipeline</Label>
      <Card>
        {Object.entries(STATUS_CONFIG).map(([k, v]) => {
          const count = byStatus[k] || 0;
          const pct = total > 0 ? Math.round((count / total) * 100) : 0;
          return (
            <div key={k} style={{ display: "flex", alignItems: "center", gap: 12, marginBottom: 12 }}>
              <div style={{ width: 8, height: 8, borderRadius: "50%", background: v.color, flexShrink: 0 }} />
              <span style={{ fontSize: 12, color: "#666", width: 70 }}>{v.label}</span>
              <div style={{ flex: 1, height: 4, background: "#1A1A28", borderRadius: 2, overflow: "hidden" }}>
                <div style={{ height: "100%", width: pct + "%", background: v.color, borderRadius: 2 }} />
              </div>
              <span style={{ fontSize: 11, color: "#444", fontFamily: "monospace", width: 24, textAlign: "right" }}>{count}</span>
            </div>
          );
        })}
      </Card>
    </div>
  );
}

// ── DASHBOARD ────────────────────────────────────────────────────────────────
function Dashboard({ apps, tasks, goals, setPage }) {
  const interviews = apps.filter(a => a.status === "interview");
  const offers = apps.filter(a => a.status === "offer");
  const urgentTasks = tasks.filter(t => !t.done && t.priority === "high");
  const responseRate = apps.length > 0
    ? Math.round(((interviews.length + offers.length) / apps.length) * 100) : 0;

  return (
    <div>
      <div style={{ marginBottom: 28 }}>
        <div style={{ fontSize: 10, color: "#333", letterSpacing: "0.2em", fontFamily: "monospace", marginBottom: 4 }}>{TODAY}</div>
        <div style={{ fontSize: 22, color: "#E0D8F0" }}>Your Job Hunt HQ</div>
        <div style={{ fontSize: 12, color: "#3A3A4A", marginTop: 4 }}>Stay focused. Stay consistent.</div>
      </div>

      <div style={{ display: "flex", gap: 10, flexWrap: "wrap", marginBottom: 18 }}>
        {[
          { label: "Applications", value: apps.length, color: ACCENT, page: "applications" },
          { label: "Interviews", value: interviews.length, color: AMBER, page: "applications" },
          { label: "Offers", value: offers.length, color: GREEN, page: "applications" },
        ].map(s => (
          <Card key={s.label} style={{ flex: 1, minWidth: 90, textAlign: "center" }} onClick={() => setPage(s.page)}>
            <div style={{ fontSize: 24, color: s.color, fontWeight: "bold" }}>{s.value}</div>
            <div style={{ fontSize: 9, color: "#444", letterSpacing: "0.12em", textTransform: "uppercase", marginTop: 4 }}>{s.label}</div>
          </Card>
        ))}
      </div>

      <Card style={{ marginBottom: 14 }}>
        <Label>Response Rate</Label>
        <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 6 }}>
          <span style={{ fontSize: 12, color: "#666" }}>Interviews + Offers / Total Applied</span>
          <span style={{ fontSize: 12, color: AMBER, fontFamily: "monospace" }}>{responseRate}%</span>
        </div>
        <ProgressBar pct={responseRate} color={AMBER} />
      </Card>

      {urgentTasks.length > 0 && (
        <Card style={{ marginBottom: 14, borderColor: RED + "33" }} onClick={() => setPage("tasks")}>
          <Label>Urgent Tasks</Label>
          {urgentTasks.slice(0, 3).map(t => (
            <div key={t.id} style={{ display: "flex", alignItems: "center", gap: 8, marginBottom: 6 }}>
              <span style={{ color: RED, fontSize: 8 }}>●</span>
              <span style={{ fontSize: 13, color: "#A098C0" }}>{t.text}</span>
            </div>
          ))}
          <div style={{ fontSize: 10, color: ACCENT, marginTop: 6 }}>View all →</div>
        </Card>
      )}

      {goals.length > 0 && (
        <Card onClick={() => setPage("goals")}>
          <Label>Goal Snapshots</Label>
          {goals.slice(0, 3).map(g => {
            const pct = Math.min(100, Math.round((g.current / g.target) * 100));
            return (
              <div key={g.id} style={{ marginBottom: 12 }}>
                <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 4 }}>
                  <span style={{ fontSize: 12, color: "#888" }}>{g.name}</span>
                  <span style={{ fontSize: 10, color: ACCENT, fontFamily: "monospace" }}>{pct}%</span>
                </div>
                <ProgressBar pct={pct} />
              </div>
            );
          })}
          <div style={{ fontSize: 10, color: ACCENT, marginTop: 2 }}>Manage goals →</div>
        </Card>
      )}
    </div>
  );
}

// ── APP ───────────────────────────────────────────────────────────────────────
export default function App() {
  const [page, setPage] = useState("dashboard");
  const [apps, setApps] = useState([
    { id: 1, company: "Stripe", role: "Product Designer", status: "interview", date: "Mar 4" },
    { id: 2, company: "Notion", role: "UX Researcher", status: "applied", date: "Mar 6" },
    { id: 3, company: "Linear", role: "Frontend Engineer", status: "wishlist", date: "Mar 7" },
    { id: 4, company: "Figma", role: "Design Ops", status: "rejected", date: "Feb 28" },
  ]);
  const [tasks, setTasks] = useState([
    { id: 1, text: "Prep for Stripe interview", done: false, priority: "high" },
    { id: 2, text: "Update portfolio with new case study", done: false, priority: "high" },
    { id: 3, text: "Cold email 5 people this week", done: true, priority: "medium" },
    { id: 4, text: "Research Linear's design system", done: false, priority: "low" },
  ]);
  const [goals, setGoals] = useState([
    { id: 1, name: "Applications sent", target: 30, current: 12, unit: "apps" },
    { id: 2, name: "Networking outreach", target: 20, current: 7, unit: "msgs" },
    { id: 3, name: "Interview practice", target: 10, current: 3, unit: "sessions" },
  ]);

  return (
    <div style={{ minHeight: "100vh", background: "#080810", color: "#E0D8F0", fontFamily: "Georgia, serif" }}>
      <div style={{
        position: "sticky", top: 0, zIndex: 10,
        background: "#080810DD", backdropFilter: "blur(12px)",
        borderBottom: "1px solid #12121C",
        padding: "0 20px", height: 50,
        display: "flex", alignItems: "center", justifyContent: "space-between",
      }}>
        <span style={{ fontSize: 11, color: "#333", letterSpacing: "0.2em", fontFamily: "monospace" }}>CAREER.OS</span>
        <span style={{ fontSize: 12, color: "#444" }}>{NAV.find(n => n.id === page).icon} {NAV.find(n => n.id === page).label}</span>
      </div>

      <div style={{ maxWidth: 620, margin: "0 auto", padding: "28px 16px 100px" }}>
        {page === "dashboard"    && <Dashboard apps={apps} tasks={tasks} goals={goals} setPage={setPage} />}
        {page === "applications" && <Applications apps={apps} setApps={setApps} />}
        {page === "tasks"        && <Tasks tasks={tasks} setTasks={setTasks} />}
        {page === "goals"        && <Goals goals={goals} setGoals={setGoals} />}
        {page === "stats"        && <Stats apps={apps} tasks={tasks} goals={goals} />}
      </div>

      <div style={{
        position: "fixed", bottom: 0, left: 0, right: 0,
        background: "#080810EE", backdropFilter: "blur(12px)",
        borderTop: "1px solid #12121C",
        display: "flex", justifyContent: "center", gap: 4,
        padding: "8px 12px 14px",
      }}>
        {NAV.map(n => (
          <button key={n.id} onClick={() => setPage(n.id)} style={{
            background: page === n.id ? ACCENT + "22" : "transparent",
            border: "1px solid " + (page === n.id ? ACCENT + "55" : "transparent"),
            color: page === n.id ? ACCENT : "#333",
            borderRadius: 10, padding: "8px 14px", cursor: "pointer",
            display: "flex", flexDirection: "column", alignItems: "center", gap: 3,
            minWidth: 50, transition: "all 0.2s",
          }}>
            <span style={{ fontSize: 15 }}>{n.icon}</span>
            <span style={{ fontSize: 9, letterSpacing: "0.08em", fontFamily: "monospace" }}>{n.label}</span>
          </button>
        ))}
      </div>
    </div>
  );
}
