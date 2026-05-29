import { useState, useEffect, useRef } from "react";

const useInView = (threshold = 0.15) => {
  const ref = useRef(null);
  const [visible, setVisible] = useState(false);
  useEffect(() => {
    const obs = new IntersectionObserver(([e]) => { if (e.isIntersecting) setVisible(true); }, { threshold });
    if (ref.current) obs.observe(ref.current);
    return () => obs.disconnect();
  }, []);
  return [ref, visible];
};

const Leaf = ({ style }) => (
  <svg viewBox="0 0 40 50" style={{ position: "absolute", opacity: 0.18, ...style }} width="32" height="40">
    <path d="M20 48 C20 48 4 32 4 18 C4 8 12 2 20 2 C28 2 36 8 36 18 C36 32 20 48 20 48Z" fill="#4a7c3f" />
    <path d="M20 48 L20 12" stroke="#2d5016" strokeWidth="1.5" fill="none" strokeLinecap="round" />
    <path d="M20 22 C14 18 10 14 12 10" stroke="#2d5016" strokeWidth="1" fill="none" strokeLinecap="round" />
    <path d="M20 30 C26 26 30 22 28 17" stroke="#2d5016" strokeWidth="1" fill="none" strokeLinecap="round" />
  </svg>
);

const FloatingLeaves = () => {
  const leaves = Array.from({ length: 9 }, (_, i) => ({
    id: i,
    left: `${8 + i * 10}%`,
    delay: `${i * 1.3}s`,
    duration: `${7 + (i % 3) * 2}s`,
    size: 22 + (i % 4) * 10,
    rotate: i % 2 === 0 ? "rotate(20deg)" : "rotate(-15deg)",
  }));
  return (
    <div style={{ position: "absolute", inset: 0, overflow: "hidden", pointerEvents: "none" }}>
      {leaves.map(l => (
        <div key={l.id} style={{
          position: "absolute", top: "-60px", left: l.left,
          animation: `floatLeaf ${l.duration} ${l.delay} ease-in-out infinite`,
          transform: l.rotate,
        }}>
          <Leaf style={{ width: l.size, height: l.size * 1.25, opacity: 0.22, position: "relative" }} />
        </div>
      ))}
    </div>
  );
};

const GlowOrb = ({ color, style }) => (
  <div style={{
    position: "absolute", borderRadius: "50%",
    background: color, filter: "blur(60px)", opacity: 0.22, pointerEvents: "none", ...style,
  }} />
);

const FeatureCard = ({ icon, title, desc, delay }) => {
  const [ref, visible] = useInView();
  return (
    <div ref={ref} style={{
      background: "rgba(255,255,255,0.055)",
      border: "1px solid rgba(180,200,140,0.18)",
      borderRadius: 20,
      padding: "28px 24px",
      backdropFilter: "blur(10px)",
      transition: `opacity 0.7s ${delay}s, transform 0.7s ${delay}s`,
      opacity: visible ? 1 : 0,
      transform: visible ? "translateY(0)" : "translateY(28px)",
    }}>
      <div style={{ fontSize: 36, marginBottom: 14 }}>{icon}</div>
      <div style={{ fontFamily: "'Playfair Display', serif", fontSize: 17, fontWeight: 600, color: "#d4e8a0", marginBottom: 10, lineHeight: 1.3 }}>{title}</div>
      <div style={{ fontSize: 14, color: "rgba(210,225,185,0.72)", lineHeight: 1.7 }}>{desc}</div>
    </div>
  );
};

const PhoneMockup = ({ label, content, delay }) => {
  const [ref, visible] = useInView();
  return (
    <div ref={ref} style={{
      transition: `opacity 0.8s ${delay}s, transform 0.8s ${delay}s`,
      opacity: visible ? 1 : 0,
      transform: visible ? "translateY(0) scale(1)" : "translateY(40px) scale(0.94)",
      display: "flex", flexDirection: "column", alignItems: "center",
    }}>
      <div style={{
        width: 180, height: 340,
        background: "linear-gradient(160deg,#1a2e10 0%,#0f1f0a 100%)",
        borderRadius: 28, border: "2px solid rgba(160,200,90,0.28)",
        boxShadow: "0 30px 60px rgba(0,0,0,0.5), 0 0 0 1px rgba(100,180,60,0.1)",
        padding: "18px 14px", display: "flex", flexDirection: "column",
        position: "relative", overflow: "hidden",
      }}>
        <div style={{ position: "absolute", top: 0, left: "30%", right: "30%", height: 20, background: "#0f1f0a", borderRadius: "0 0 12px 12px", zIndex: 2 }} />
        <div style={{ position: "absolute", top: 6, left: "50%", transform: "translateX(-50%)", width: 40, height: 4, background: "rgba(200,220,160,0.15)", borderRadius: 10, zIndex: 3 }} />
        <GlowOrb color="#4a9020" style={{ width: 80, height: 80, top: -20, right: -20 }} />
        <div style={{ marginTop: 16, flex: 1 }}>{content}</div>
      </div>
      <div style={{ marginTop: 12, fontSize: 12, color: "rgba(200,220,160,0.5)", letterSpacing: 1, textTransform: "uppercase" }}>{label}</div>
    </div>
  );
};

const SectionHeading = ({ sup, title, sub }) => {
  const [ref, visible] = useInView();
  return (
    <div ref={ref} style={{ textAlign: "center", marginBottom: 52, transition: "opacity 0.8s, transform 0.8s", opacity: visible ? 1 : 0, transform: visible ? "translateY(0)" : "translateY(24px)" }}>
      <div style={{ fontSize: 11, letterSpacing: 3, color: "#a8c870", textTransform: "uppercase", marginBottom: 14, fontWeight: 600 }}>{sup}</div>
      <h2 style={{ fontFamily: "'Playfair Display', serif", fontSize: "clamp(26px,5vw,42px)", fontWeight: 700, color: "#e8f5c8", margin: "0 0 16px", lineHeight: 1.25 }}>{title}</h2>
      {sub && <p style={{ fontSize: 15, color: "rgba(210,225,185,0.65)", maxWidth: 480, margin: "0 auto", lineHeight: 1.7 }}>{sub}</p>}
    </div>
  );
};

export default function KrishiAI() {
  const [scrollY, setScrollY] = useState(0);
  const [heroVisible, setHeroVisible] = useState(false);

  useEffect(() => {
    setHeroVisible(true);
    const onScroll = () => setScrollY(window.scrollY);
    window.addEventListener("scroll", onScroll, { passive: true });
    return () => window.removeEventListener("scroll", onScroll);
  }, []);

  const features = [
    { icon: "🔍", title: "AI Crop Disease Detection", desc: "Scan any crop or leaf with your camera. KrishiAI analyzes the image and identifies diseases, pest attacks, fungal infections, and nutrient deficiencies — then gives you clear treatment guidance.", delay: 0 },
    { icon: "🌾", title: "AI Farming Assistant", desc: "Ask farming questions in simple language. Get guidance on crops, fertilizers, watering schedules, pest control, seasonal farming, and soil care — available anytime, day or night.", delay: 0.1 },
    { icon: "🌤️", title: "Weather Insights", desc: "Stay ahead of the weather. Understand when to irrigate, when to spray, and how to protect your crops from sudden temperature changes and rainfall.", delay: 0.2 },
    { icon: "📋", title: "Smart Diagnosis Reports", desc: "After each crop scan, receive a well-organized report showing the disease name, severity level, step-by-step treatment, and prevention tips — written simply for farmers.", delay: 0.3 },
    { icon: "🤝", title: "Farmer Community", desc: "Connect with other farmers. Share crop photos, discuss problems, ask questions, and learn together in a supportive farming community built for real growers.", delay: 0.4 },
    { icon: "📁", title: "Saved Farming History", desc: "Every scan and conversation is saved. Review past diagnoses, track recurring crop issues, and build a personal farming record that helps you grow smarter each season.", delay: 0.5 },
  ];

  const phoneScreens = [
    {
      label: "Crop Scanner", delay: 0,
      content: (
        <div>
          <div style={{ fontSize: 11, color: "#a8c870", letterSpacing: 1, marginBottom: 8 }}>SCAN RESULT</div>
          <div style={{ background: "rgba(80,160,40,0.15)", border: "1px solid rgba(120,200,60,0.25)", borderRadius: 12, padding: "10px 12px", marginBottom: 10 }}>
            <div style={{ fontSize: 10, color: "#c8e090", fontWeight: 600 }}>Leaf Blight Detected</div>
            <div style={{ fontSize: 9, color: "rgba(180,210,130,0.7)", marginTop: 3 }}>Severity: Moderate</div>
            <div style={{ display: "flex", gap: 4, marginTop: 6 }}>
              {["Fungal", "Moisture"].map(t => (
                <span key={t} style={{ fontSize: 8, background: "rgba(100,180,50,0.2)", color: "#a8d060", padding: "2px 6px", borderRadius: 20 }}>{t}</span>
              ))}
            </div>
          </div>
          <div style={{ fontSize: 10, color: "#a0c870", marginBottom: 6, fontWeight: 600 }}>Treatment Steps</div>
          {["Apply copper fungicide", "Remove affected leaves", "Reduce leaf wetness"].map((s, i) => (
            <div key={i} style={{ display: "flex", gap: 6, alignItems: "flex-start", marginBottom: 5 }}>
              <div style={{ width: 4, height: 4, borderRadius: "50%", background: "#78b040", flexShrink: 0, marginTop: 4 }} />
              <div style={{ fontSize: 9, color: "rgba(190,215,150,0.75)" }}>{s}</div>
            </div>
          ))}
        </div>
      )
    },
    {
      label: "AI Assistant", delay: 0.15,
      content: (
        <div style={{ display: "flex", flexDirection: "column", gap: 8 }}>
          <div style={{ fontSize: 11, color: "#a8c870", letterSpacing: 1, marginBottom: 4 }}>AI ASSISTANT</div>
          <div style={{ background: "rgba(255,255,255,0.06)", borderRadius: "4px 12px 12px 12px", padding: "8px 10px" }}>
            <div style={{ fontSize: 9, color: "rgba(190,215,150,0.8)", lineHeight: 1.5 }}>When should I water my wheat crop?</div>
          </div>
          <div style={{ background: "rgba(80,160,40,0.15)", borderRadius: "12px 12px 12px 4px", padding: "8px 10px", border: "1px solid rgba(100,180,50,0.15)" }}>
            <div style={{ fontSize: 9, color: "rgba(200,230,160,0.85)", lineHeight: 1.55 }}>Water wheat in early morning or evening. During flowering stage, maintain consistent moisture. Avoid overwatering — check if top 2 inches of soil feel dry.</div>
          </div>
          <div style={{ background: "rgba(255,255,255,0.06)", borderRadius: "4px 12px 12px 12px", padding: "8px 10px" }}>
            <div style={{ fontSize: 9, color: "rgba(190,215,150,0.8)" }}>What about fertilizer?</div>
          </div>
          <div style={{ background: "rgba(80,160,40,0.15)", borderRadius: "12px 12px 12px 4px", padding: "8px 10px", border: "1px solid rgba(100,180,50,0.15)" }}>
            <div style={{ fontSize: 9, color: "rgba(200,230,160,0.85)", lineHeight: 1.55 }}>Apply nitrogen fertilizer in 3 splits — at sowing, tillering, and jointing stage for best yield results.</div>
          </div>
        </div>
      )
    },
    {
      label: "Weather View", delay: 0.3,
      content: (
        <div>
          <div style={{ fontSize: 11, color: "#a8c870", letterSpacing: 1, marginBottom: 10 }}>WEATHER INSIGHTS</div>
          <div style={{ textAlign: "center", marginBottom: 14 }}>
            <div style={{ fontSize: 32, marginBottom: 4 }}>🌤️</div>
            <div style={{ fontSize: 22, fontWeight: 700, color: "#d8f090" }}>27°C</div>
            <div style={{ fontSize: 10, color: "rgba(180,210,130,0.7)" }}>Partly cloudy · Low wind</div>
          </div>
          {[
            { label: "Rain Chance", val: "18%", icon: "💧" },
            { label: "Humidity", val: "62%", icon: "🌫️" },
            { label: "Best for spray", val: "Yes", icon: "✅" },
          ].map(r => (
            <div key={r.label} style={{ display: "flex", justifyContent: "space-between", alignItems: "center", padding: "6px 0", borderBottom: "1px solid rgba(140,200,80,0.1)" }}>
              <div style={{ fontSize: 9, color: "rgba(190,215,150,0.7)" }}>{r.icon} {r.label}</div>
              <div style={{ fontSize: 10, color: "#c0e080", fontWeight: 600 }}>{r.val}</div>
            </div>
          ))}
        </div>
      )
    }
  ];

  return (
    <div style={{
      background: "#0a160a",
      minHeight: "100vh",
      fontFamily: "'DM Sans', system-ui, sans-serif",
      color: "#e0f0c0",
      overflowX: "hidden",
    }}>
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=Playfair+Display:wght@600;700;800&family=DM+Sans:wght@300;400;500;600&display=swap');
        @keyframes floatLeaf {
          0%,100% { transform: translateY(0) rotate(20deg) scale(1); }
          50% { transform: translateY(calc(100vh + 80px)) rotate(-10deg) scale(0.85); }
        }
        @keyframes fadeUp {
          from { opacity:0; transform:translateY(30px); }
          to { opacity:1; transform:translateY(0); }
        }
        @keyframes glow {
          0%,100% { opacity:0.18; transform:scale(1); }
          50% { opacity:0.28; transform:scale(1.08); }
        }
        @keyframes pulse {
          0%,100% { opacity:0.6; }
          50% { opacity:1; }
        }
        @keyframes logoFloat {
          0%,100% { transform:translateY(0px); }
          50% { transform:translateY(-8px); }
        }
        .krishi-btn {
          background: linear-gradient(135deg, #4a8f20, #2d6010);
          color: #d8f0a0;
          border: 1px solid rgba(140,210,80,0.35);
          padding: 15px 36px;
          borderRadius: 40px;
          fontSize: 15px;
          fontWeight: 600;
          cursor: pointer;
          letterSpacing: 0.5px;
          transition: all 0.3s ease;
          fontFamily: "'DM Sans', sans-serif";
          display: inline-flex;
          alignItems: center;
          gap: 10px;
        }
        .krishi-btn:hover {
          transform: translateY(-2px);
          box-shadow: 0 12px 40px rgba(80,160,40,0.35);
          border-color: rgba(160,220,100,0.5);
        }
        * { box-sizing: border-box; margin: 0; padding: 0; }
        html { scroll-behavior: smooth; }
      `}</style>

      {/* HERO */}
      <section style={{ position: "relative", minHeight: "100vh", display: "flex", alignItems: "center", justifyContent: "center", overflow: "hidden", padding: "80px 20px 60px" }}>
        {/* Background layers */}
        <div style={{ position: "absolute", inset: 0, background: "radial-gradient(ellipse 80% 70% at 50% 30%, #0d2808 0%, #0a160a 60%, #050c04 100%)" }} />
        <GlowOrb color="#386020" style={{ width: 500, height: 400, top: "-10%", left: "10%", animation: "glow 6s ease-in-out infinite" }} />
        <GlowOrb color="#1a4a08" style={{ width: 400, height: 400, bottom: "-10%", right: "5%", animation: "glow 8s 2s ease-in-out infinite" }} />
        <GlowOrb color="#5a8030" style={{ width: 200, height: 200, top: "40%", left: "50%", transform: "translate(-50%,-50%)", animation: "glow 5s 1s ease-in-out infinite" }} />

        {/* Floating field lines */}
        <div style={{ position: "absolute", bottom: 0, left: 0, right: 0, height: 200, background: "linear-gradient(0deg, rgba(10,20,6,0.95) 0%, transparent 100%)" }} />

        <FloatingLeaves />

        {/* Hero content */}
        <div style={{ position: "relative", zIndex: 2, textAlign: "center", maxWidth: 680, margin: "0 auto" }}>
          <div style={{ animation: heroVisible ? "fadeUp 0.8s 0.1s both" : "none" }}>
            <div style={{ display: "flex", justifyContent: "center", marginBottom: 28 }}>
              <div style={{ animation: "logoFloat 4s ease-in-out infinite", position: "relative" }}>
                <div style={{ position: "absolute", inset: -12, borderRadius: "50%", background: "radial-gradient(circle, rgba(100,200,60,0.2) 0%, transparent 70%)", animation: "glow 3s ease-in-out infinite" }} />
                <img
                  src="/api/placeholder/100/100"
                  alt="KrishiAI Logo"
                  style={{ width: 88, height: 88, borderRadius: "50%", display: "none" }}
                />
                {/* Logo SVG recreation */}
                <svg width="88" height="88" viewBox="0 0 88 88">
                  <defs>
                    <radialGradient id="orb" cx="50%" cy="40%" r="55%">
                      <stop offset="0%" stopColor="#c8a020" />
                      <stop offset="60%" stopColor="#a07010" />
                      <stop offset="100%" stopColor="#6a4800" />
                    </radialGradient>
                    <radialGradient id="bg" cx="50%" cy="50%" r="50%">
                      <stop offset="0%" stopColor="#1a3a10" />
                      <stop offset="100%" stopColor="#0d2008" />
                    </radialGradient>
                  </defs>
                  <circle cx="44" cy="44" r="42" fill="url(#bg)" stroke="rgba(180,220,80,0.3)" strokeWidth="1.5" />
                  <circle cx="44" cy="40" r="28" fill="url(#orb)" opacity="0.85" />
                  <circle cx="44" cy="40" r="28" fill="none" stroke="rgba(220,200,100,0.4)" strokeWidth="1" />
                  <path d="M44 18 C44 18 30 28 30 38 C30 44 36 48 44 48 C52 48 58 44 58 38 C58 28 44 18 44 18Z" fill="#1e3810" stroke="#8ab040" strokeWidth="1.5" />
                  <path d="M44 20 L44 48" stroke="#6a9030" strokeWidth="1.5" strokeLinecap="round" />
                  <path d="M44 28 C40 24 36 24 34 26" stroke="#6a9030" strokeWidth="1.2" fill="none" strokeLinecap="round" />
                  <path d="M44 34 C48 30 52 30 54 32" stroke="#6a9030" strokeWidth="1.2" fill="none" strokeLinecap="round" />
                  <path d="M32 52 C28 56 28 60 32 62 L56 62 C60 60 60 56 56 52 Z" fill="#1e3810" stroke="#8ab040" strokeWidth="1.5" />
                </svg>
              </div>
            </div>
          </div>

          <div style={{ animation: heroVisible ? "fadeUp 0.8s 0.25s both" : "none" }}>
            <div style={{ fontSize: 11, letterSpacing: 4, color: "#90b860", textTransform: "uppercase", marginBottom: 14, fontWeight: 600 }}>Agriculture · Intelligence · Growth</div>
            <h1 style={{ fontFamily: "'Playfair Display', serif", fontSize: "clamp(48px,10vw,80px)", fontWeight: 800, color: "#e8f5c0", lineHeight: 1.08, marginBottom: 18, letterSpacing: -1 }}>
              Krishi<span style={{ color: "#a0d040" }}>AI</span>
            </h1>
            <p style={{ fontSize: "clamp(18px,4vw,24px)", color: "rgba(210,235,170,0.8)", fontWeight: 300, marginBottom: 24, letterSpacing: 0.5 }}>Smart AI assistant for farmers</p>
            <p style={{ fontSize: 15, color: "rgba(190,215,150,0.62)", lineHeight: 1.85, maxWidth: 520, margin: "0 auto 40px", fontWeight: 300 }}>
              KrishiAI helps farmers with crop disease understanding, farming guidance, weather information and AI-powered farming assistance — designed for the real needs of everyday growers.
            </p>
          </div>

          <div style={{ animation: heroVisible ? "fadeUp 0.8s 0.45s both" : "none" }}>
            <button className="krishi-btn" style={{
              background: "linear-gradient(135deg, #4a8f20, #2d6010)",
              color: "#d8f0a0",
              border: "1px solid rgba(140,210,80,0.35)",
              padding: "15px 36px",
              borderRadius: 40,
              fontSize: 15,
              fontWeight: 600,
              cursor: "pointer",
              letterSpacing: 0.5,
              fontFamily: "'DM Sans', sans-serif",
              display: "inline-flex",
              alignItems: "center",
              gap: 10,
              transition: "all 0.3s ease",
            }}
              onMouseEnter={e => { e.currentTarget.style.transform = "translateY(-2px)"; e.currentTarget.style.boxShadow = "0 12px 40px rgba(80,160,40,0.35)"; }}
              onMouseLeave={e => { e.currentTarget.style.transform = "translateY(0)"; e.currentTarget.style.boxShadow = "none"; }}
            >
              <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round">
                <path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2z" />
                <path d="M10 8.5v7l5.5-3.5z" fill="currentColor" stroke="none" />
              </svg>
              Download from Play Store
            </button>
            <div style={{ marginTop: 16, fontSize: 12, color: "rgba(160,200,100,0.45)", letterSpacing: 0.5 }}>Free to download · Android</div>
          </div>

          {/* Scroll hint */}
          <div style={{ marginTop: 60, animation: "pulse 2.5s ease-in-out infinite" }}>
            <svg width="24" height="36" viewBox="0 0 24 36" fill="none" style={{ margin: "0 auto", display: "block" }}>
              <rect x="1" y="1" width="22" height="34" rx="11" stroke="rgba(140,200,80,0.3)" strokeWidth="1.5" />
              <rect x="10" y="7" width="4" height="8" rx="2" fill="rgba(140,200,80,0.5)" />
            </svg>
          </div>
        </div>
      </section>

      {/* FEATURES */}
      <section style={{ padding: "90px 20px", position: "relative", overflow: "hidden" }}>
        <div style={{ position: "absolute", inset: 0, background: "linear-gradient(180deg, #0a160a 0%, #071208 50%, #0a160a 100%)" }} />
        <GlowOrb color="#2a5010" style={{ width: 600, height: 300, top: "30%", left: "-10%", opacity: 0.15 }} />
        <div style={{ position: "relative", zIndex: 1, maxWidth: 860, margin: "0 auto" }}>
          <SectionHeading
            sup="What KrishiAI Can Do"
            title="Built for the real farmer"
            sub="Every feature in KrishiAI is designed around what actual farmers need — practical, clear, and easy to use on any Android phone."
          />
          <div style={{ display: "grid", gridTemplateColumns: "repeat(auto-fit, minmax(260px, 1fr))", gap: 20 }}>
            {features.map((f, i) => <FeatureCard key={i} {...f} />)}
          </div>
        </div>
      </section>

      {/* APP EXPERIENCE */}
      <section style={{ padding: "90px 20px", position: "relative", overflow: "hidden" }}>
        <div style={{ position: "absolute", inset: 0, background: "radial-gradient(ellipse 100% 60% at 50% 50%, #0d2008 0%, #080f06 100%)" }} />
        <GlowOrb color="#386030" style={{ width: 400, height: 300, bottom: "10%", right: "5%", opacity: 0.12 }} />
        <div style={{ position: "relative", zIndex: 1, maxWidth: 860, margin: "0 auto" }}>
          <SectionHeading
            sup="App Experience"
            title="Farming intelligence in your pocket"
            sub="A calm, clean interface designed specifically for farmers. Large text, simple navigation, and smooth experience — even on low-end Android phones."
          />
          <div style={{ display: "flex", justifyContent: "center", gap: 28, flexWrap: "wrap" }}>
            {phoneScreens.map((p, i) => <PhoneMockup key={i} {...p} />)}
          </div>
        </div>
      </section>

      {/* VISION */}
      <section style={{ padding: "90px 20px 60px", position: "relative", overflow: "hidden" }}>
        <div style={{ position: "absolute", inset: 0, background: "linear-gradient(180deg, #080f06 0%, #0c1a08 50%, #0a160a 100%)" }} />
        <FloatingLeaves />
        <GlowOrb color="#4a7820" style={{ width: 500, height: 500, top: "50%", left: "50%", transform: "translate(-50%,-50%)", opacity: 0.1 }} />
        <div style={{ position: "relative", zIndex: 1, maxWidth: 640, margin: "0 auto", textAlign: "center" }}>
          {(() => {
            const [ref, visible] = useInView();
            return (
              <div ref={ref} style={{ transition: "opacity 0.9s, transform 0.9s", opacity: visible ? 1 : 0, transform: visible ? "translateY(0)" : "translateY(30px)" }}>
                <div style={{ fontSize: 11, letterSpacing: 3, color: "#90b860", textTransform: "uppercase", marginBottom: 20, fontWeight: 600 }}>Our Vision</div>
                <div style={{ width: 48, height: 2, background: "rgba(140,200,80,0.4)", margin: "0 auto 32px", borderRadius: 2 }} />
                <h2 style={{ fontFamily: "'Playfair Display', serif", fontSize: "clamp(28px,6vw,46px)", fontWeight: 700, color: "#e0f0c0", lineHeight: 1.25, marginBottom: 26 }}>
                  Technology that grows alongside the farmer
                </h2>
                <p style={{ fontSize: 16, color: "rgba(195,220,155,0.68)", lineHeight: 1.9, marginBottom: 20 }}>
                  KrishiAI aims to make modern AI farming assistance easier, simpler and more accessible — through intelligent guidance, smart tools, and a deep respect for the farmer's daily reality.
                </p>
                <p style={{ fontSize: 15, color: "rgba(175,205,135,0.52)", lineHeight: 1.85 }}>
                  We believe every farmer deserves the same quality of agricultural knowledge that modern science can offer — in their language, on their phone, at the right moment.
                </p>
              </div>
            );
          })()}
        </div>
      </section>

      {/* DESIGNED FOR FARMERS */}
      <section style={{ padding: "70px 20px 90px", position: "relative" }}>
        <div style={{ position: "absolute", inset: 0, background: "#071208" }} />
        <div style={{ position: "relative", zIndex: 1, maxWidth: 860, margin: "0 auto" }}>
          <SectionHeading
            sup="Designed With Care"
            title="Made for every farmer"
          />
          <div style={{ display: "grid", gridTemplateColumns: "repeat(auto-fit, minmax(220px, 1fr))", gap: 16 }}>
            {[
              { icon: "📱", title: "Large readable text", desc: "Comfortable to read even in bright sunlight outdoors." },
              { icon: "🗺️", title: "Simple navigation", desc: "Everything is easy to find — no confusion, no clutter." },
              { icon: "⚡", title: "Low-end phone ready", desc: "Optimized to run smoothly even on affordable Android devices." },
              { icon: "🔒", title: "Secure accounts", desc: "Your farming data stays private with secure cloud storage." },
            ].map((item, i) => {
              const [ref, visible] = useInView();
              return (
                <div key={i} ref={ref} style={{
                  background: "rgba(255,255,255,0.035)",
                  border: "1px solid rgba(140,200,80,0.12)",
                  borderRadius: 16,
                  padding: "22px 20px",
                  transition: `opacity 0.7s ${i * 0.1}s, transform 0.7s ${i * 0.1}s`,
                  opacity: visible ? 1 : 0,
                  transform: visible ? "translateY(0)" : "translateY(20px)",
                }}>
                  <div style={{ fontSize: 28, marginBottom: 12 }}>{item.icon}</div>
                  <div style={{ fontFamily: "'DM Sans', sans-serif", fontSize: 14, fontWeight: 600, color: "#c8e898", marginBottom: 8 }}>{item.title}</div>
                  <div style={{ fontSize: 13, color: "rgba(190,215,150,0.6)", lineHeight: 1.6 }}>{item.desc}</div>
                </div>
              );
            })}
          </div>
        </div>
      </section>

      {/* FOOTER */}
      <footer style={{ padding: "40px 20px", borderTop: "1px solid rgba(100,160,60,0.1)", position: "relative" }}>
        <div style={{ position: "absolute", inset: 0, background: "#060e05" }} />
        <div style={{ position: "relative", zIndex: 1, maxWidth: 860, margin: "0 auto", display: "flex", justifyContent: "space-between", alignItems: "center", flexWrap: "wrap", gap: 16 }}>
          <div style={{ display: "flex", alignItems: "center", gap: 10 }}>
            <svg width="28" height="28" viewBox="0 0 88 88">
              <defs>
                <radialGradient id="fg" cx="50%" cy="40%" r="55%">
                  <stop offset="0%" stopColor="#c8a020" />
                  <stop offset="100%" stopColor="#6a4800" />
                </radialGradient>
              </defs>
              <circle cx="44" cy="44" r="42" fill="#0d2008" stroke="rgba(140,200,60,0.25)" strokeWidth="1" />
              <circle cx="44" cy="40" r="26" fill="url(#fg)" opacity="0.8" />
              <path d="M44 20 C44 20 32 30 32 38 C32 44 37 48 44 48 C51 48 56 44 56 38 C56 30 44 20 44 20Z" fill="#1e3810" stroke="#7a9830" strokeWidth="1.2" />
              <path d="M44 22 L44 48" stroke="#6a9030" strokeWidth="1.2" strokeLinecap="round" />
            </svg>
            <span style={{ fontFamily: "'Playfair Display', serif", fontSize: 18, fontWeight: 700, color: "#c0e080" }}>KrishiAI</span>
          </div>
          <div style={{ fontSize: 12, color: "rgba(160,200,100,0.4)", textAlign: "center" }}>
            Smart farming assistance · Built with care for farmers
          </div>
          <div style={{ fontSize: 12, color: "rgba(140,180,80,0.35)" }}>© 2025 KrishiAI</div>
        </div>
      </footer>
    </div>
  );
}
