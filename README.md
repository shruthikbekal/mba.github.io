import React, { useState } from 'react';
import './App.css';
import { 
  TrendingUp, 
  Users, 
  Clock, 
  AlertCircle, 
  ChevronRight, 
  CheckCircle,
  Star,
  DollarSign,
  Zap,
  ArrowLeft,
  Info
} from 'lucide-react';

const INITIAL_STATE = {
  currentStageIndex: 0,
  subStage: 'main', // 'main', 'analysis', or 'tactical'
  brandAwareness: 0, // Set to 0 as the brand is yet to be launched
  customerLoyalty: 0, // Set to 0 as the brand is yet to be launched
  marketShare: 0, // Set to 0 as the brand is yet to be launched
  profitMargin: 0, // Set to 0 as the brand is yet to be launched
  brandEquity: 0, // Set to 0 as the brand is yet to be launched
  logs: ["Welcome, Brand Manager. Phase 1: Brand Launch."],
  selectedTacticalIds: []
};

const STAGES = [
  {
    id: "launch",
    title: "Phase 1: Brand Launch",
    description: "You're launching a new consumer brand. The market is crowded, and consumers are skeptical. How will you position your brand to stand out?",
    mainOptions: [
      {
        id: "premium",
        label: "Premium Positioning",
        analysis: "You aim to create a luxury brand image, focusing on quality, exclusivity, and high margins. This builds strong brand equity but limits initial market penetration.",
        opportunityCost: "By ignoring mass-market appeal, you miss out on quick volume sales and broad awareness. Your brand may be seen as elitist and inaccessible.",
        tacticalTitle: "Launch Tactics",
        tacticalDescription: "Select up to TWO tactics to execute your premium launch strategy.",
        tacticalOptions: [
          { id: "influencer", label: "Influencer Partnerships", impact: { awareness: 15, loyalty: 10, share: 2, margin: 5, equity: 20000 }, desc: "Partner with luxury influencers for authentic endorsements." },
          { id: "exclusive", label: "Exclusive Retail Channels", impact: { awareness: 10, loyalty: 15, share: 1, margin: 10, equity: 30000 }, desc: "Launch only in high-end boutiques and online luxury stores." },
          { id: "quality_focus", label: "Superior Quality Materials", impact: { awareness: 5, loyalty: 20, share: 0, margin: 8, equity: 25000 }, desc: "Use premium materials despite higher costs." },
          { id: "storytelling", label: "Brand Storytelling Campaign", impact: { awareness: 20, loyalty: 5, share: 3, margin: 0, equity: 15000 }, desc: "Create compelling narratives around brand heritage." }
        ]
      },
      {
        id: "value",
        label: "Value Positioning",
        analysis: "You focus on affordability and accessibility, aiming for mass-market adoption. This drives quick sales volume but may dilute brand perception.",
        opportunityCost: "By sacrificing premium pricing, you risk being perceived as low-quality. Building loyalty and equity will be harder in a price-sensitive market.",
        tacticalTitle: "Launch Tactics",
        tacticalDescription: "Select up to TWO tactics to execute your value-driven launch strategy.",
        tacticalOptions: [
          { id: "mass_media", label: "Mass Media Advertising", impact: { awareness: 25, loyalty: -5, share: 5, margin: -5, equity: 5000 }, desc: "Heavy TV and social media campaigns for broad reach." },
          { id: "discount", label: "Launch Discounts", impact: { awareness: 15, loyalty: -10, share: 8, margin: -10, equity: -5000 }, desc: "Introductory pricing to drive initial sales." },
          { id: "omni_channel", label: "Omni-Channel Distribution", impact: { awareness: 10, loyalty: 5, share: 6, margin: 2, equity: 10000 }, desc: "Sell through supermarkets, online, and discount stores." },
          { id: "viral", label: "Viral Social Campaign", impact: { awareness: 30, loyalty: 0, share: 4, margin: 0, equity: 8000 }, desc: "User-generated content challenges for organic growth." }
        ]
      }
    ]
  },
  {
    id: "extension",
    title: "Phase 2: Brand Extension",
    description: "Your brand has gained traction. Competitors are copying your success. Should you extend into related product categories or focus on core strengthening?",
    mainOptions: [
      {
        id: "aggressive_extension",
        label: "Aggressive Extension",
        analysis: "You rapidly expand into adjacent categories to capture market share and leverage existing brand equity. This maximizes revenue potential but risks brand dilution.",
        opportunityCost: "By spreading thin, you may weaken your core brand identity. Quality control becomes challenging across multiple product lines.",
        tacticalTitle: "Extension Tactics",
        tacticalDescription: "Select up to TWO tactics to manage your aggressive expansion.",
        tacticalOptions: [
          { id: "line_extension", label: "Product Line Extensions", impact: { awareness: 10, loyalty: 5, share: 8, margin: 3, equity: 15000 }, desc: "Add variants like flavors, sizes, or colors." },
          { id: "category_extension", label: "Category Extensions", impact: { awareness: 15, loyalty: -5, share: 12, margin: 0, equity: 10000 }, desc: "Enter completely new product categories." },
          { id: "licensing", label: "Brand Licensing", impact: { awareness: 20, loyalty: -10, share: 6, margin: 5, equity: 8000 }, desc: "License brand to other companies for co-branded products." },
          { id: "acquisitions", label: "Strategic Acquisitions", impact: { awareness: 5, loyalty: 10, share: 10, margin: 2, equity: 20000 }, desc: "Buy smaller brands in target categories." }
        ]
      },
      {
        id: "core_focus",
        label: "Core Brand Focus",
        analysis: "You concentrate on strengthening your flagship products and deepening customer relationships. This builds enduring brand equity but limits growth potential.",
        opportunityCost: "By not expanding, you leave market share vulnerable to competitors. Revenue growth may be slower, but brand integrity is preserved.",
        tacticalTitle: "Focus Tactics",
        tacticalDescription: "Select up to TWO tactics to strengthen your core brand.",
        tacticalOptions: [
          { id: "innovation", label: "Product Innovation", impact: { awareness: 5, loyalty: 15, share: 2, margin: 8, equity: 25000 }, desc: "Invest heavily in R&D for breakthrough features." },
          { id: "customer_loyalty", label: "Loyalty Programs", impact: { awareness: 0, loyalty: 25, share: 1, margin: 5, equity: 15000 }, desc: "Create rewards programs for repeat customers." },
          { id: "brand_experience", label: "Enhanced Brand Experience", impact: { awareness: 10, loyalty: 20, share: 0, margin: 3, equity: 20000 }, desc: "Improve packaging, service, and customer touchpoints." },
          { id: "premium_pricing", label: "Price Optimization", impact: { awareness: -5, loyalty: 10, share: -2, margin: 15, equity: 10000 }, desc: "Gradually increase prices as brand equity grows." }
        ]
      }
    ]
  },
  {
    id: "crisis",
    title: "Phase 3: Crisis Management",
    description: "A major scandal has hit your brand - product quality issues or unethical practices exposed. Public trust is eroding. How do you respond?",
    mainOptions: [
      {
        id: "transparent",
        label: "Transparent Accountability",
        analysis: "You take full responsibility, communicate openly, and implement comprehensive fixes. This rebuilds trust but may involve significant short-term costs.",
        opportunityCost: "Full transparency may amplify negative publicity initially. Legal and financial repercussions could be severe, but long-term recovery is possible.",
        tacticalTitle: "Recovery Tactics",
        tacticalDescription: "Select up to TWO tactics to execute transparent crisis management.",
        tacticalOptions: [
          { id: "public_apology", label: "Public Apology Campaign", impact: { awareness: 10, loyalty: -15, share: -5, margin: 0, equity: -20000 }, desc: "CEO apology across all media channels." },
          { id: "recall", label: "Product Recall", impact: { awareness: 5, loyalty: 5, share: -8, margin: -10, equity: -15000 }, desc: "Voluntarily recall affected products." },
          { id: "independent_audit", label: "Independent Audit", impact: { awareness: 0, loyalty: 10, share: 0, margin: -5, equity: 5000 }, desc: "Hire third-party auditors to verify fixes." },
          { id: "compensation", label: "Customer Compensation", impact: { awareness: 15, loyalty: 20, share: -3, margin: -8, equity: 10000 }, desc: "Offer refunds and compensation to affected customers." }
        ]
      },
      {
        id: "defensive",
        label: "Defensive Posture",
        analysis: "You take a defensive stance, minimizing admissions and focusing on legal protections. This may protect short-term financials but prolong the crisis.",
        opportunityCost: "Defensiveness often backfires, leading to worse public perception. Loyal customers may abandon the brand, and recovery takes longer.",
        tacticalTitle: "Defense Tactics",
        tacticalDescription: "Select up to TWO tactics to maintain a defensive stance.",
        tacticalOptions: [
          { id: "legal_action", label: "Legal Countermeasures", impact: { awareness: 5, loyalty: -20, share: -10, margin: 0, equity: -30000 }, desc: "Sue accusers and media for defamation." },
          { id: "spin_control", label: "PR Spin Campaign", impact: { awareness: 10, loyalty: -10, share: -2, margin: 2, equity: -10000 }, desc: "Hire crisis PR firm to reframe the narrative." },
          { id: "minimal_recall", label: "Minimal Fixes", impact: { awareness: -5, loyalty: -5, share: -5, margin: 5, equity: -5000 }, desc: "Make only required changes to avoid liability." },
          { id: "loyalty_discounts", label: "Loyalty Discounts", impact: { awareness: 0, loyalty: 5, share: 2, margin: -5, equity: 0 }, desc: "Offer discounts to retain existing customers." }
        ]
      }
    ]
  }
];

export default function App() {
  const [game, setGame] = useState(INITIAL_STATE);
  const [gameOver, setGameOver] = useState(false);
  const [selectedMain, setSelectedMain] = useState(null);

  const handleMainChoice = (option) => {
    setSelectedMain(option);
    setGame(prev => ({ ...prev, subStage: 'analysis' }));
  };

  const startTacticalPhase = () => {
    setGame(prev => ({ ...prev, subStage: 'tactical', selectedTacticalIds: [] }));
  };

  const toggleTacticalOption = (id) => {
    setGame(prev => {
      const current = prev.selectedTacticalIds;
      if (current.includes(id)) {
        return { ...prev, selectedTacticalIds: current.filter(x => x !== id) };
      }
      if (current.length < 2) {
        return { ...prev, selectedTacticalIds: [...current, id] };
      }
      return prev;
    });
  };

  const applyDecisions = () => {
    const tacticalOptions = selectedMain.tacticalOptions.filter(o => 
      game.selectedTacticalIds.includes(o.id)
    );

    let aw = 0, loy = 0, sh = 0, mar = 0, eq = 0;
    const feedbackArr = [];

    tacticalOptions.forEach(opt => {
      aw += (opt.impact.awareness || 0);
      loy += (opt.impact.loyalty || 0);
      sh += (opt.impact.share || 0);
      mar += (opt.impact.margin || 0);
      eq += (opt.impact.equity || 0);
      feedbackArr.push(opt.label);
    });

    const nextIdx = game.currentStageIndex + 1;
    const isOver = nextIdx >= STAGES.length;

    setGame(prev => ({
      ...prev,
      currentStageIndex: nextIdx,
      subStage: 'main',
      brandAwareness: Math.min(100, Math.max(0, prev.brandAwareness + aw)),
      customerLoyalty: Math.min(100, Math.max(0, prev.customerLoyalty + loy)),
      marketShare: Math.min(50, Math.max(0, prev.marketShare + sh)),
      profitMargin: Math.min(50, Math.max(0, prev.profitMargin + mar)),
      brandEquity: Math.max(0, prev.brandEquity + eq),
      logs: [`Executed: ${feedbackArr.join(" + ")}`, ...prev.logs].slice(0, 5),
      selectedTacticalIds: []
    }));

    if (isOver) setGameOver(true);
    setSelectedMain(null);
  };

  const currentStage = STAGES[game.currentStageIndex];

  return (
    <div className="min-h-screen bg-slate-950 text-slate-100 p-4 md:p-8 font-sans selection:bg-indigo-500/30">
      <div className="max-w-6xl mx-auto">
        
        {/* Dashboard Header */}
        <header className="flex flex-col lg:flex-row gap-6 mb-8">
          <div className="bg-slate-900 border border-slate-800 p-6 rounded-2xl flex-1 border-b-4 border-b-indigo-500">
            <h1 className="text-2xl font-black flex items-center gap-2 mb-1">
              <Star className="text-indigo-400" /> BRAND EMPIRE
            </h1>
            <p className="text-slate-500 text-xs font-bold uppercase tracking-widest italic">Brand Management Simulation v1.0</p>
          </div>
          
          <div className="grid grid-cols-2 md:grid-cols-4 gap-4 flex-[3]">
            <MetricCard label="Awareness" value={`${game.brandAwareness}%`} icon={<Users size={14}/>} color="text-blue-400" />
            <MetricCard label="Loyalty" value={`${game.customerLoyalty}%`} icon={<Zap size={14}/>} color="text-yellow-400" />
            <MetricCard label="Market Share" value={`${game.marketShare}%`} icon={<TrendingUp size={14}/>} color="text-green-400" />
            <MetricCard label="Profit Margin" value={`${game.profitMargin}%`} icon={<DollarSign size={14}/>} color="text-emerald-400" />
          </div>
        </header>

        {!gameOver ? (
          <div className="grid grid-cols-1 lg:grid-cols-3 gap-8">
            <div className="lg:col-span-2 space-y-6">
              
              {/* Main Content Area */}
              <div className="bg-slate-900 border border-slate-800 rounded-3xl p-8 shadow-2xl min-h-[400px] flex flex-col justify-between">
                
                {game.subStage === 'main' && (
                  <div className="animate-in fade-in slide-in-from-bottom-4 duration-500">
                    <span className="bg-indigo-500/10 text-indigo-400 px-3 py-1 rounded-full text-[10px] font-black uppercase mb-4 inline-block tracking-widest">
                      Strategic Phase {game.currentStageIndex + 1}
                    </span>
                    <h2 className="text-3xl font-black mb-4 tracking-tight">{currentStage.title}</h2>
                    <p className="text-slate-400 text-lg leading-relaxed mb-10">{currentStage.description}</p>
                    
                    <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
                      {currentStage.mainOptions.map(opt => (
                        <button 
                          key={opt.id}
                          onClick={() => handleMainChoice(opt)}
                          className="p-6 rounded-2xl border-2 border-slate-800 bg-slate-800/50 hover:border-indigo-500 hover:bg-indigo-500/5 transition-all text-left group"
                        >
                          <div className="flex justify-between items-center mb-4">
                            <span className="text-[10px] font-black text-slate-500 uppercase tracking-widest group-hover:text-indigo-400 transition-colors">Select Strategy</span>
                            <ChevronRight className="text-slate-700 group-hover:text-indigo-400 group-hover:translate-x-1 transition-all" />
                          </div>
                          <h4 className="text-xl font-bold leading-tight">{opt.label}</h4>
                        </button>
                      ))}
                    </div>
                  </div>
                )}

                {game.subStage === 'tactical' && (
                  <div className="animate-in fade-in slide-in-from-right-4 duration-500">
                    <div className="flex justify-between items-start mb-6">
                      <div>
                        <h2 className="text-2xl font-black">{selectedMain.tacticalTitle}</h2>
                        <p className="text-slate-500 text-sm">{selectedMain.tacticalDescription}</p>
                      </div>
                      <div className="bg-indigo-500/20 text-indigo-400 px-4 py-2 rounded-xl text-xs font-black">
                        SELECTED: {game.selectedTacticalIds.length} / 2
                      </div>
                    </div>

                    <div className="grid grid-cols-1 md:grid-cols-2 gap-4 mb-8">
                      {selectedMain.tacticalOptions.map(opt => {
                        const isSelected = game.selectedTacticalIds.includes(opt.id);
                        return (
                          <button
                            key={opt.id}
                            onClick={() => toggleTacticalOption(opt.id)}
                            className={`p-5 rounded-2xl border-2 transition-all text-left flex flex-col justify-between gap-2 ${
                              isSelected 
                              ? 'border-indigo-500 bg-indigo-500/10' 
                              : 'border-slate-800 bg-slate-900/50 hover:border-slate-700'
                            }`}
                          >
                            <div className="flex justify-between items-center">
                              <span className="font-bold text-white">{opt.label}</span>
                              {isSelected && <CheckCircle size={18} className="text-indigo-400" />}
                            </div>
                            <p className="text-xs text-slate-500">{opt.desc}</p>
                          </button>
                        );
                      })}
                    </div>

                    <div className="flex gap-4">
                       <button 
                        onClick={() => setGame(p => ({...p, subStage: 'main'}))}
                        className="px-6 py-3 rounded-xl border border-slate-700 text-slate-400 font-bold text-sm flex items-center gap-2 hover:bg-slate-800 transition-colors"
                      >
                        <ArrowLeft size={16} /> Back
                      </button>
                      <button 
                        disabled={game.selectedTacticalIds.length === 0}
                        onClick={applyDecisions}
                        className={`flex-1 py-4 rounded-xl font-black text-sm uppercase tracking-widest transition-all ${
                          game.selectedTacticalIds.length > 0
                          ? 'bg-indigo-600 hover:bg-indigo-500 shadow-lg shadow-indigo-500/20'
                          : 'bg-slate-800 text-slate-600 cursor-not-allowed'
                        }`}
                      >
                        Execute Strategy
                      </button>
                    </div>
                  </div>
                )}
              </div>
              
              {/* Progress */}
              <div className="flex items-center gap-4 text-[10px] font-black text-slate-600 tracking-tighter uppercase">
                <span>Brand Lifecycle Progress</span>
                <div className="flex-1 h-1 bg-slate-900 rounded-full flex gap-1 overflow-hidden">
                  {STAGES.map((_, i) => (
                    <div key={i} className={`flex-1 h-full transition-all duration-1000 ${i <= game.currentStageIndex ? 'bg-indigo-500' : 'bg-slate-800'}`} />
                  ))}
                </div>
              </div>
            </div>

            {/* Sidebar Stats */}
            <aside className="space-y-6">
              <div className="bg-slate-900 border border-slate-800 rounded-3xl p-6 flex flex-col gap-6">
                <h3 className="text-[10px] font-black text-slate-500 uppercase tracking-widest flex items-center gap-2">
                  <Clock size={14} /> Brand Log
                </h3>
                <div className="space-y-3 flex-1 min-h-[150px]">
                  {game.logs.map((log, i) => (
                    <div key={i} className={`p-3 rounded-xl text-xs border-l-2 transition-all ${i === 0 ? 'bg-indigo-500/10 border-indigo-400 text-slate-100' : 'bg-slate-950/50 border-slate-800 text-slate-500'}`}>
                      {log}
                    </div>
                  ))}
                </div>
                
                <div className="pt-6 border-t border-slate-800 space-y-4">
                   <div className="space-y-1">
                    <div className="flex justify-between text-[10px] font-black text-slate-500 uppercase">
                      <span>Brand Equity</span>
                      <span className={game.brandEquity < 50000 ? 'text-red-400' : 'text-emerald-400'}>${game.brandEquity.toLocaleString()}</span>
                    </div>
                    <div className="h-1.5 bg-slate-950 rounded-full overflow-hidden">
                      <div className={`h-full transition-all duration-700 ${game.brandEquity < 50000 ? 'bg-red-500' : 'bg-emerald-500'}`} style={{width: `${Math.min(100, (game.brandEquity / 200000) * 100)}%`}}></div>
                    </div>
                   </div>
                </div>
              </div>
            </aside>
          </div>
        ) : (
          /* Report Card */
          <div className="bg-slate-900 border border-slate-800 rounded-[3rem] p-16 text-center animate-in zoom-in duration-700">
            <div className="inline-flex p-6 bg-indigo-500/10 rounded-full mb-8">
              <CheckCircle size={64} className="text-indigo-400" />
            </div>
            <h2 className="text-5xl font-black mb-4 italic tracking-tighter">BRAND LIFECYCLE COMPLETE</h2>
            <p className="text-slate-500 mb-12 max-w-lg mx-auto">You've navigated the challenges of brand management. Here is your final brand valuation.</p>
            
            <div className="grid grid-cols-1 md:grid-cols-3 gap-6 max-w-4xl mx-auto mb-16">
              <FinalScore label="Brand Awareness" value={`${game.brandAwareness}%`} color="text-blue-400" sub="Market Recognition" />
              <FinalScore label="Customer Loyalty" value={`${game.customerLoyalty}%`} color="text-yellow-400" sub="Retention & Advocacy" />
              <FinalScore label="Market Leadership" value={`${game.marketShare}%`} color="text-green-400" sub="Competitive Position" />
            </div>

            <div className="bg-slate-950 p-10 rounded-3xl border border-slate-800 max-w-2xl mx-auto text-left mb-12">
              <h4 className="font-black text-xl mb-4 flex items-center gap-2"><TrendingUp size={24} className="text-indigo-400"/> Brand Strategy Debrief</h4>
              <p className="text-slate-400 leading-relaxed text-sm">
                Your brand management approach balanced <strong>short-term gains</strong> with <strong>long-term equity building</strong>. 
                {game.customerLoyalty > 70 ? " You prioritized customer relationships, creating a loyal following that will sustain growth." : " Your focus on rapid expansion built awareness but may have sacrificed customer loyalty."} 
                Remember: Strong brands are built on consistent value delivery and authentic customer connections.
              </p>
            </div>

            <button 
              onClick={() => {setGame(INITIAL_STATE); setGameOver(false);}}
              className="px-12 py-5 bg-indigo-600 hover:bg-indigo-500 text-white font-black rounded-2xl transition-all shadow-[0_20px_50px_rgba(99,102,241,0.3)] uppercase tracking-widest text-sm"
            >
              Relaunch Brand
            </button>
          </div>
        )}

        {/* Consequence Modal */}
        {game.subStage === 'analysis' && selectedMain && (
          <div className="fixed inset-0 z-50 flex items-center justify-center p-4">
            <div className="absolute inset-0 bg-slate-950/80 backdrop-blur-md" />
            <div className="relative bg-slate-900 border border-slate-700 w-full max-w-2xl rounded-[2.5rem] shadow-2xl p-10 animate-in fade-in zoom-in duration-300">
              <div className="flex items-center gap-3 text-indigo-400 mb-6">
                <div className="p-3 bg-indigo-500/10 rounded-2xl">
                  <Info size={28} />
                </div>
                <h3 className="text-2xl font-black">Strategic Analysis</h3>
              </div>
              
              <div className="space-y-8 mb-10">
                <section>
                  <h5 className="text-[10px] font-black text-indigo-400 uppercase tracking-widest mb-2">Strategy Implications</h5>
                  <p className="text-slate-200 text-lg leading-relaxed">{selectedMain.analysis}</p>
                </section>
                
                <section className="bg-slate-950 p-6 rounded-2xl border border-slate-800">
                  <h5 className="text-[10px] font-black text-red-400 uppercase tracking-widest mb-2 flex items-center gap-1.5">
                    <AlertCircle size={10} /> Strategic Trade-off
                  </h5>
                  <p className="text-slate-400 italic text-sm leading-relaxed">{selectedMain.opportunityCost}</p>
                </section>
              </div>

              <button 
                onClick={startTacticalPhase}
                className="w-full py-5 bg-indigo-600 hover:bg-indigo-500 text-white font-black rounded-2xl transition-all flex items-center justify-center gap-2 group"
              >
                PROCEED TO TACTICAL EXECUTION
                <ChevronRight className="group-hover:translate-x-1 transition-transform" />
              </button>
            </div>
          </div>
        )}

      </div>
      <footer className="mt-12 text-center text-slate-700 text-[10px] font-bold uppercase tracking-[0.3em] py-8">
        Brand Management Simulation © 2026<br />
        Dr Shruthi K Bekal
      </footer>
    </div>
  );
}

function MetricCard({ label, value, icon, color }) {
  return (
    <div className="bg-slate-900 border border-slate-800 p-4 rounded-2xl">
      <div className="flex items-center gap-2 text-slate-600 text-[10px] font-black uppercase mb-1 tracking-tighter">
        {icon} {label}
      </div>
      <div className={`text-2xl font-black ${color}`}>{value}</div>
    </div>
  );
}

function FinalScore({ label, value, color, sub }) {
  return (
    <div className="bg-slate-950 border border-slate-800 p-8 rounded-[2rem]">
      <div className="text-slate-500 text-[10px] font-black uppercase tracking-widest mb-2">{label}</div>
      <div className={`text-4xl font-black mb-1 ${color}`}>{value}</div>
      <div className="text-[10px] text-slate-700 font-bold italic uppercase">{sub}</div>
    </div>
  );
}
