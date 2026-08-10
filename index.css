import React, { useState, useMemo } from "react";

// ---------- Tax reference data (2025 tax year, simplified) ----------

const FEDERAL_BRACKETS = {
  single: [
    [0, 11925, 0.10], [11925, 48475, 0.12], [48475, 103350, 0.22],
    [103350, 197300, 0.24], [197300, 250525, 0.32], [250525, 626350, 0.35], [626350, Infinity, 0.37]
  ],
  mfj: [
    [0, 23850, 0.10], [23850, 96950, 0.12], [96950, 206700, 0.22],
    [206700, 394600, 0.24], [394600, 501050, 0.32], [501050, 751600, 0.35], [751600, Infinity, 0.37]
  ],
  hoh: [
    [0, 17000, 0.10], [17000, 64850, 0.12], [64850, 103350, 0.22],
    [103350, 197300, 0.24], [197300, 250500, 0.32], [250500, 626350, 0.35], [626350, Infinity, 0.37]
  ],
  mfs: [
    [0, 11925, 0.10], [11925, 48475, 0.12], [48475, 103350, 0.22],
    [103350, 197300, 0.24], [197300, 250525, 0.32], [250525, 375800, 0.35], [375800, Infinity, 0.37]
  ]
};

const STANDARD_DEDUCTION = { single: 15000, mfj: 30000, hoh: 22500, mfs: 15000 };
const CHILD_TAX_CREDIT_PER_DEPENDENT = 2000;
const CTC_PHASEOUT_START = { single: 200000, mfj: 400000, hoh: 200000, mfs: 200000 };

// ---------- State-specific tax logic: NY, NJ, TX ----------
// These three are modeled with real 2025 brackets. Other states are intentionally
// not included yet — see the note in the UI when a user needs a state outside this list.

const NY_BRACKETS = {
  single: [
    [0, 8500, 0.04], [8500, 11700, 0.045], [11700, 13900, 0.0525], [13900, 80650, 0.055],
    [80650, 215400, 0.06], [215400, 1077550, 0.0685], [1077550, 5000000, 0.0965],
    [5000000, 25000000, 0.103], [25000000, Infinity, 0.109]
  ],
  mfj: [
    [0, 17150, 0.04], [17150, 23600, 0.045], [23600, 27900, 0.0525], [27900, 161550, 0.055],
    [161550, 323200, 0.06], [323200, 2155350, 0.0685], [2155350, 5000000, 0.0965],
    [5000000, 25000000, 0.103], [25000000, Infinity, 0.109]
  ],
  hoh: [
    [0, 12800, 0.04], [12800, 17650, 0.045], [17650, 20900, 0.0525], [20900, 107650, 0.055],
    [107650, 269300, 0.06], [269300, 1616450, 0.0685], [1616450, 5000000, 0.0965],
    [5000000, 25000000, 0.103], [25000000, Infinity, 0.109]
  ],
  mfs: [ // NY taxes MFS like single brackets
    [0, 8500, 0.04], [8500, 11700, 0.045], [11700, 13900, 0.0525], [13900, 80650, 0.055],
    [80650, 215400, 0.06], [215400, 1077550, 0.0685], [1077550, 5000000, 0.0965],
    [5000000, 25000000, 0.103], [25000000, Infinity, 0.109]
  ]
};
const NY_STANDARD_DEDUCTION = { single: 8000, mfj: 16050, hoh: 11200, mfs: 8000 };

// NYC local income tax, approximate marginal rates layered on NY taxable income
const NYC_BRACKETS = {
  single: [[0, 12000, 0.03078], [12000, 25000, 0.03762], [25000, 50000, 0.03819], [50000, Infinity, 0.03876]],
  mfj: [[0, 21600, 0.03078], [21600, 45000, 0.03762], [45000, 90000, 0.03819], [90000, Infinity, 0.03876]],
  hoh: [[0, 14400, 0.03078], [14400, 30000, 0.03762], [30000, 60000, 0.03819], [60000, Infinity, 0.03876]],
  mfs: [[0, 12000, 0.03078], [12000, 25000, 0.03762], [25000, 50000, 0.03819], [50000, Infinity, 0.03876]]
};

const NJ_BRACKETS = {
  single: [
    [0, 20000, 0.014], [20000, 35000, 0.0175], [35000, 40000, 0.035], [40000, 75000, 0.05525],
    [75000, 500000, 0.0637], [500000, 1000000, 0.0897], [1000000, Infinity, 0.1075]
  ],
  mfj: [
    [0, 20000, 0.014], [20000, 50000, 0.0175], [50000, 70000, 0.0245], [70000, 80000, 0.035],
    [80000, 150000, 0.05525], [150000, 500000, 0.0637], [500000, 1000000, 0.0897], [1000000, Infinity, 0.1075]
  ],
  hoh: [
    [0, 20000, 0.014], [20000, 50000, 0.0175], [50000, 70000, 0.0245], [70000, 80000, 0.035],
    [80000, 150000, 0.05525], [150000, 500000, 0.0637], [500000, 1000000, 0.0897], [1000000, Infinity, 0.1075]
  ],
  mfs: [
    [0, 20000, 0.014], [20000, 35000, 0.0175], [35000, 40000, 0.035], [40000, 75000, 0.05525],
    [75000, 500000, 0.0637], [500000, 1000000, 0.0897], [1000000, Infinity, 0.1075]
  ]
};
// NJ has no standard deduction; instead flat personal exemptions
const NJ_EXEMPTION = { single: 1000, mfj: 2000, hoh: 1000, mfs: 1000 };
const NJ_DEPENDENT_EXEMPTION = 1500;

const STATE_OPTIONS = ["New York", "New Jersey", "Texas"];

// ---------- Fallback flat-rate approximations for all other states ----------
// These are rough effective-rate stand-ins (not real brackets/deductions) for states
// not yet fully modeled. Always shown as a low-confidence estimate in the UI.
const NO_INCOME_TAX_STATES = ["Alaska", "Florida", "Nevada", "New Hampshire", "South Dakota", "Tennessee", "Washington", "Wyoming"];

const FALLBACK_STATE_RATES = {
  "Alabama": 0.05, "Alaska": 0, "Arizona": 0.025, "Arkansas": 0.039, "California": 0.093,
  "Colorado": 0.044, "Connecticut": 0.0699, "Delaware": 0.066, "Florida": 0,
  "Georgia": 0.0519, "Hawaii": 0.079, "Idaho": 0.053, "Illinois": 0.0495, "Indiana": 0.03,
  "Iowa": 0.038, "Kansas": 0.057, "Kentucky": 0.04, "Louisiana": 0.03,
  "Maine": 0.0715, "Maryland": 0.0575, "Massachusetts": 0.05, "Michigan": 0.0425,
  "Minnesota": 0.0985, "Mississippi": 0.044, "Missouri": 0.048, "Montana": 0.059,
  "Nebraska": 0.052, "Nevada": 0, "New Hampshire": 0, "New Mexico": 0.059,
  "North Carolina": 0.0425, "North Dakota": 0.025, "Ohio": 0.035, "Oklahoma": 0.045,
  "Oregon": 0.099, "Pennsylvania": 0.0307, "Rhode Island": 0.0599,
  "South Carolina": 0.06, "South Dakota": 0, "Tennessee": 0, "Utah": 0.0455,
  "Vermont": 0.0875, "Virginia": 0.0575, "Washington": 0, "West Virginia": 0.0482,
  "Wisconsin": 0.0765, "Wyoming": 0, "District of Columbia": 0.0895
};

const ALL_OTHER_STATES = Object.keys(FALLBACK_STATE_RATES).sort();

const PAY_FREQUENCIES = {
  "Weekly": 52, "Biweekly": 26, "Semi-monthly": 24, "Monthly": 12
};

function calcBracketTax(taxableIncome, brackets) {
  let tax = 0;
  for (const [lo, hi, rate] of brackets) {
    if (taxableIncome > lo) {
      tax += (Math.min(taxableIncome, hi) - lo) * rate;
    } else break;
  }
  return tax;
}

function estimateFederalTax({ grossIncome, filingStatus, dependents }) {
  const deduction = STANDARD_DEDUCTION[filingStatus];
  const taxable = Math.max(0, grossIncome - deduction);
  let tax = calcBracketTax(taxable, FEDERAL_BRACKETS[filingStatus]);

  // Child Tax Credit, simplified phaseout
  let ctc = dependents * CHILD_TAX_CREDIT_PER_DEPENDENT;
  const phaseoutStart = CTC_PHASEOUT_START[filingStatus];
  if (grossIncome > phaseoutStart) {
    const over = grossIncome - phaseoutStart;
    const reduction = Math.ceil(over / 1000) * 50;
    ctc = Math.max(0, ctc - reduction);
  }
  tax = Math.max(0, tax - ctc);
  return { tax, ctc, taxable };
}

function estimateStateTax({ grossIncome, filingStatus, dependents, stateName, isNYC }) {
  if (stateName === "Texas") {
    return { tax: 0, confidence: "high" };
  }

  if (stateName === "New York") {
    const deduction = NY_STANDARD_DEDUCTION[filingStatus];
    const taxable = Math.max(0, grossIncome - deduction);
    const stateTax = calcBracketTax(taxable, NY_BRACKETS[filingStatus]);
    let nycTax = 0;
    if (isNYC) {
      nycTax = calcBracketTax(taxable, NYC_BRACKETS[filingStatus]);
    }
    return { tax: stateTax + nycTax, confidence: "high" };
  }

  if (stateName === "New Jersey") {
    const exemption = NJ_EXEMPTION[filingStatus] + dependents * NJ_DEPENDENT_EXEMPTION;
    const taxable = Math.max(0, grossIncome - exemption);
    const tax = calcBracketTax(taxable, NJ_BRACKETS[filingStatus]);
    return { tax, confidence: "high" };
  }

  // Fallback: rough flat-rate approximation for any other state
  const rate = FALLBACK_STATE_RATES[stateName];
  if (rate === undefined) return { tax: 0, confidence: "none" };
  return { tax: grossIncome * rate, confidence: "low" };
}

function fmt(n) {
  return n.toLocaleString("en-US", { style: "currency", currency: "USD", maximumFractionDigits: 0 });
}

export default function WithholdingTracker() {
  const [step, setStep] = useState("form");
  const [filingStatus, setFilingStatus] = useState("single");
  const [dependents, setDependents] = useState(0);
  const [stateName, setStateName] = useState("");
  const [isNYC, setIsNYC] = useState(false);
  const [showOtherStates, setShowOtherStates] = useState(false);
  const [requestSubmitted, setRequestSubmitted] = useState(false);
  const [requestEmail, setRequestEmail] = useState("");

  const [incomeMode, setIncomeMode] = useState("know"); // "know" | "calculate"
  const [grossIncome, setGrossIncome] = useState("");

  // Fields for calculating income from a paycheck
  const [recentGrossPay, setRecentGrossPay] = useState("");
  const [ytdGrossPay, setYtdGrossPay] = useState("");
  const [projectionMethod, setProjectionMethod] = useState("consistent"); // "consistent" | "average"

  const [payFrequency, setPayFrequency] = useState("Biweekly");
  const [payPeriodsElapsed, setPayPeriodsElapsed] = useState("");
  const [ytdFederalWithheld, setYtdFederalWithheld] = useState("");
  const [ytdStateWithheld, setYtdStateWithheld] = useState("");

  const totalPeriods = PAY_FREQUENCIES[payFrequency];

  const calculatedAnnualIncome = useMemo(() => {
    const periodsElapsed = Number(payPeriodsElapsed);
    if (!periodsElapsed) return null;

    if (projectionMethod === "consistent") {
      const recent = Number(recentGrossPay);
      if (!recent) return null;
      return recent * totalPeriods;
    } else {
      const ytd = Number(ytdGrossPay);
      if (!ytd) return null;
      return (ytd / periodsElapsed) * totalPeriods;
    }
  }, [projectionMethod, recentGrossPay, ytdGrossPay, payPeriodsElapsed, totalPeriods]);

  const effectiveIncome = incomeMode === "know" ? Number(grossIncome) : (calculatedAnnualIncome || 0);

  const incomeReady =
    incomeMode === "know"
      ? Number(grossIncome) > 0
      : calculatedAnnualIncome !== null && calculatedAnnualIncome > 0;

  const canSubmit =
    stateName &&
    incomeReady &&
    Number(payPeriodsElapsed) > 0 &&
    Number(payPeriodsElapsed) <= totalPeriods &&
    ytdFederalWithheld !== "" &&
    ytdStateWithheld !== "";

  const results = useMemo(() => {
    if (step !== "results") return null;
    const income = effectiveIncome;
    const periodsElapsed = Number(payPeriodsElapsed);
    const fedWithheldSoFar = Number(ytdFederalWithheld);
    const stateWithheldSoFar = Number(ytdStateWithheld);

    const { tax: fedLiability, ctc } = estimateFederalTax({
      grossIncome: income, filingStatus, dependents: Number(dependents)
    });
    const { tax: stateLiability, confidence: stateConfidence } = estimateStateTax({
      grossIncome: income, filingStatus, dependents: Number(dependents), stateName, isNYC
    });

    const perPeriodFed = fedWithheldSoFar / periodsElapsed;
    const perPeriodState = stateWithheldSoFar / periodsElapsed;
    const projectedFed = perPeriodFed * totalPeriods;
    const projectedState = perPeriodState * totalPeriods;

    const periodsRemaining = totalPeriods - periodsElapsed;

    const fedGap = fedLiability - projectedFed;
    const stateGap = stateLiability - projectedState;

    const fedRaisePerCheck = periodsRemaining > 0 ? fedGap / periodsRemaining : fedGap;
    const stateRaisePerCheck = periodsRemaining > 0 ? stateGap / periodsRemaining : stateGap;

    return {
      income, fedLiability, stateLiability, ctc,
      projectedFed, projectedState,
      fedGap, stateGap,
      fedRaisePerCheck, stateRaisePerCheck,
      periodsRemaining, totalPeriods, periodsElapsed,
      fedOnTrack: fedGap <= 0,
      stateOnTrack: stateGap <= 0,
      hasStateTax: stateName !== "Texas" && stateName !== "" && !NO_INCOME_TAX_STATES.includes(stateName),
      stateConfidence
    };
  }, [step, effectiveIncome, payPeriodsElapsed, ytdFederalWithheld, ytdStateWithheld, stateName, isNYC, filingStatus, dependents, totalPeriods]);

  // FORMSPREE_ENDPOINT: replace with your own Formspree form URL.
  // See README.md step "Connect the state request form" for how to get one (it's free).
  const FORMSPREE_ENDPOINT = "https://formspree.io/f/YOUR_FORM_ID";

  async function submitStateRequest() {
    try {
      await fetch(FORMSPREE_ENDPOINT, {
        method: "POST",
        headers: { "Content-Type": "application/json", Accept: "application/json" },
        body: JSON.stringify({
          state_requested: stateName || "unknown",
          email: requestEmail || "not provided",
          timestamp: new Date().toISOString()
        })
      });
      setRequestSubmitted(true);
    } catch (e) {
      // Fail gracefully so the calculator itself never breaks over this
      setRequestSubmitted(true);
    }
  }

  const inputCls =
    "w-full bg-transparent border-b-2 border-[#0F2A3D]/20 focus:border-[#B5482E] outline-none py-2 text-lg font-serif text-[#0F2A3D] placeholder:text-[#0F2A3D]/30 transition-colors";
  const labelCls = "block text-xs uppercase tracking-[0.12em] text-[#0F2A3D]/60 font-sans font-semibold mb-1";

  return (
    <div className="min-h-screen bg-[#F7F4EE] text-[#0F2A3D]" style={{ fontFamily: "'Source Serif 4', Georgia, serif" }}>
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=Source+Serif+4:wght@400;600;700&family=Inter:wght@400;500;600;700&display=swap');
        .font-sans { font-family: 'Inter', system-ui, sans-serif; }
        .font-serif { font-family: 'Source Serif 4', Georgia, serif; }
        input[type=number]::-webkit-inner-spin-button { opacity: 1; }
      `}</style>

      <div className="max-w-xl mx-auto px-6 py-12">
        <header className="mb-10">
          <div className="font-sans text-xs uppercase tracking-[0.2em] text-[#B5482E] font-bold mb-2">
            Sterling Tax Group
          </div>
          <h1 className="font-serif text-3xl sm:text-4xl font-semibold leading-tight">
            Will your withholding<br />cover you by December?
          </h1>
          <p className="font-sans text-sm text-[#0F2A3D]/60 mt-3 leading-relaxed">
            Enter your info from a recent pay stub. We'll project where your withholding lands by year end and tell you exactly what to adjust if it falls short.
          </p>
        </header>

        {step === "form" && (
          <div className="space-y-7">
            <div>
              <label className={labelCls}>Filing status</label>
              <div className="grid grid-cols-2 gap-2 font-sans">
                {[
                  ["single", "Single"], ["mfj", "Married filing jointly"],
                  ["hoh", "Head of household"], ["mfs", "Married filing separately"]
                ].map(([val, label]) => (
                  <button
                    key={val}
                    onClick={() => setFilingStatus(val)}
                    className={`text-sm py-2 px-3 rounded border text-left transition-colors ${
                      filingStatus === val
                        ? "bg-[#0F2A3D] text-[#F7F4EE] border-[#0F2A3D]"
                        : "border-[#0F2A3D]/20 hover:border-[#0F2A3D]/50"
                    }`}
                  >
                    {label}
                  </button>
                ))}
              </div>
            </div>

            <div>
              <label className={labelCls}>State</label>
              <div className="grid grid-cols-3 gap-2 font-sans">
                {STATE_OPTIONS.map((s) => (
                  <button
                    key={s}
                    onClick={() => { setStateName(s); if (s !== "New York") setIsNYC(false); setShowOtherStates(false); }}
                    className={`text-sm py-2 rounded border transition-colors ${
                      stateName === s
                        ? "bg-[#0F2A3D] text-[#F7F4EE] border-[#0F2A3D]"
                        : "border-[#0F2A3D]/20 hover:border-[#0F2A3D]/50"
                    }`}
                  >
                    {s}
                  </button>
                ))}
              </div>
              <button
                onClick={() => setShowOtherStates(!showOtherStates)}
                className={`font-sans text-sm w-full text-left py-2 px-3 mt-2 rounded border transition-colors ${
                  showOtherStates || (stateName && !STATE_OPTIONS.includes(stateName))
                    ? "bg-[#0F2A3D] text-[#F7F4EE] border-[#0F2A3D]"
                    : "border-[#0F2A3D]/20 hover:border-[#0F2A3D]/50"
                }`}
              >
                {stateName && !STATE_OPTIONS.includes(stateName) ? `Selected: ${stateName}` : "Other state"}
              </button>

              {showOtherStates && (
                <div className="mt-3 bg-[#0F2A3D]/5 rounded p-3">
                  <select
                    value={stateName && !STATE_OPTIONS.includes(stateName) ? stateName : ""}
                    onChange={(e) => setStateName(e.target.value)}
                    className="w-full bg-white border border-[#0F2A3D]/20 rounded px-3 py-2 font-sans text-sm outline-none focus:border-[#B5482E]"
                  >
                    <option value="" disabled>Select your state</option>
                    {ALL_OTHER_STATES.map((s) => (
                      <option key={s} value={s}>{s}</option>
                    ))}
                  </select>
                  <p className="font-sans text-xs text-[#0F2A3D]/60 mt-2 leading-relaxed">
                    These states use a rough estimated rate, not your state's real brackets. Want us to build full support?
                  </p>
                  {!requestSubmitted ? (
                    <div className="flex gap-2 mt-2">
                      <input
                        type="email" value={requestEmail}
                        onChange={(e) => setRequestEmail(e.target.value)}
                        placeholder="Email (optional)"
                        className="flex-1 bg-white border border-[#0F2A3D]/20 rounded px-3 py-2 font-sans text-sm outline-none focus:border-[#B5482E]"
                      />
                      <button
                        onClick={submitStateRequest}
                        disabled={!stateName || STATE_OPTIONS.includes(stateName)}
                        className="font-sans text-sm bg-[#B5482E] text-[#F7F4EE] px-4 rounded disabled:opacity-30 hover:bg-[#9c3c25] transition-colors"
                      >
                        Request
                      </button>
                    </div>
                  ) : (
                    <p className="font-sans text-sm text-[#3D7A5C] mt-2 font-semibold">
                      Thanks — request logged.
                    </p>
                  )}
                </div>
              )}

              {stateName === "New York" && (
                <label className="flex items-center gap-2 mt-3 font-sans text-sm cursor-pointer">
                  <input
                    type="checkbox" checked={isNYC}
                    onChange={(e) => setIsNYC(e.target.checked)}
                    className="accent-[#B5482E] w-4 h-4"
                  />
                  I live in New York City (adds NYC local tax)
                </label>
              )}
            </div>

            <div>
              <label className={labelCls}>Number of dependents</label>
              <input
                type="number" min="0" value={dependents}
                onChange={(e) => setDependents(e.target.value)}
                className={inputCls} placeholder="0"
              />
            </div>

            <div>
              <label className={labelCls}>Do you know your total income for the year?</label>
              <div className="grid grid-cols-2 gap-2 font-sans mb-4">
                <button
                  onClick={() => setIncomeMode("know")}
                  className={`text-sm py-2 rounded border transition-colors ${
                    incomeMode === "know"
                      ? "bg-[#0F2A3D] text-[#F7F4EE] border-[#0F2A3D]"
                      : "border-[#0F2A3D]/20 hover:border-[#0F2A3D]/50"
                  }`}
                >
                  Yes, I'll enter it
                </button>
                <button
                  onClick={() => setIncomeMode("calculate")}
                  className={`text-sm py-2 rounded border transition-colors ${
                    incomeMode === "calculate"
                      ? "bg-[#0F2A3D] text-[#F7F4EE] border-[#0F2A3D]"
                      : "border-[#0F2A3D]/20 hover:border-[#0F2A3D]/50"
                  }`}
                >
                  No, calculate it for me
                </button>
              </div>

              {incomeMode === "know" ? (
                <input
                  type="number" value={grossIncome}
                  onChange={(e) => setGrossIncome(e.target.value)}
                  className={inputCls} placeholder="$65,000"
                />
              ) : (
                <div className="space-y-4 bg-[#0F2A3D]/5 rounded p-4">
                  <div className="font-sans text-xs uppercase tracking-[0.1em] text-[#0F2A3D]/60 font-semibold mb-1">
                    Choose how to project it
                  </div>
                  <div className="grid grid-cols-1 gap-2 font-sans">
                    <button
                      onClick={() => setProjectionMethod("consistent")}
                      className={`text-sm py-2 px-3 rounded border text-left transition-colors ${
                        projectionMethod === "consistent"
                          ? "bg-[#0F2A3D] text-[#F7F4EE] border-[#0F2A3D]"
                          : "border-[#0F2A3D]/20 hover:border-[#0F2A3D]/50 bg-white"
                      }`}
                    >
                      My pay is consistent — use one recent paycheck
                    </button>
                    <button
                      onClick={() => setProjectionMethod("average")}
                      className={`text-sm py-2 px-3 rounded border text-left transition-colors ${
                        projectionMethod === "average"
                          ? "bg-[#0F2A3D] text-[#F7F4EE] border-[#0F2A3D]"
                          : "border-[#0F2A3D]/20 hover:border-[#0F2A3D]/50 bg-white"
                      }`}
                    >
                      My pay varies — average my year-to-date pay
                    </button>
                  </div>

                  {projectionMethod === "consistent" ? (
                    <div>
                      <label className={labelCls}>Gross pay on one recent paycheck</label>
                      <input
                        type="number" value={recentGrossPay}
                        onChange={(e) => setRecentGrossPay(e.target.value)}
                        className={inputCls + " bg-white px-2 border-b-0 border rounded"}
                        placeholder="e.g. $2,400"
                      />
                    </div>
                  ) : (
                    <div>
                      <label className={labelCls}>Total gross pay so far this year (YTD)</label>
                      <input
                        type="number" value={ytdGrossPay}
                        onChange={(e) => setYtdGrossPay(e.target.value)}
                        className={inputCls + " bg-white px-2 border-b-0 border rounded"}
                        placeholder="e.g. $31,000"
                      />
                      <p className="font-sans text-xs text-[#0F2A3D]/50 mt-1">
                        Uses the pay periods elapsed you enter below to find your average.
                      </p>
                    </div>
                  )}

                  {calculatedAnnualIncome !== null && (
                    <div className="font-serif text-sm pt-2 border-t border-[#0F2A3D]/10">
                      Projected annual income: <strong>{fmt(calculatedAnnualIncome)}</strong>
                    </div>
                  )}
                </div>
              )}
            </div>

            <div>
              <label className={labelCls}>Pay frequency</label>
              <div className="grid grid-cols-4 gap-2 font-sans">
                {Object.keys(PAY_FREQUENCIES).map((f) => (
                  <button
                    key={f}
                    onClick={() => setPayFrequency(f)}
                    className={`text-xs py-2 rounded border transition-colors ${
                      payFrequency === f
                        ? "bg-[#0F2A3D] text-[#F7F4EE] border-[#0F2A3D]"
                        : "border-[#0F2A3D]/20 hover:border-[#0F2A3D]/50"
                    }`}
                  >
                    {f}
                  </button>
                ))}
              </div>
            </div>

            <div>
              <label className={labelCls}>
                Pay periods elapsed so far this year (of {totalPeriods})
              </label>
              <input
                type="number" min="1" max={totalPeriods} value={payPeriodsElapsed}
                onChange={(e) => setPayPeriodsElapsed(e.target.value)}
                className={inputCls} placeholder="e.g. 15"
              />
            </div>

            <div className="grid grid-cols-2 gap-6">
              <div>
                <label className={labelCls}>YTD federal tax withheld</label>
                <input
                  type="number" value={ytdFederalWithheld}
                  onChange={(e) => setYtdFederalWithheld(e.target.value)}
                  className={inputCls} placeholder="$0"
                />
              </div>
              <div>
                <label className={labelCls}>YTD state tax withheld</label>
                <input
                  type="number" value={ytdStateWithheld}
                  onChange={(e) => setYtdStateWithheld(e.target.value)}
                  className={inputCls} placeholder="$0"
                />
              </div>
            </div>

            <button
              disabled={!canSubmit}
              onClick={() => setStep("results")}
              className="font-sans w-full bg-[#B5482E] text-[#F7F4EE] font-semibold py-3.5 rounded mt-4 disabled:opacity-30 disabled:cursor-not-allowed hover:bg-[#9c3c25] transition-colors"
            >
              See my projection
            </button>
          </div>
        )}

        {step === "results" && results && (
          <ResultsView
            results={results}
            stateName={stateName}
            isNYC={isNYC}
            onBack={() => setStep("form")}
          />
        )}
      </div>
    </div>
  );
}

function Gauge({ label, projected, liability, onTrack }) {
  const pct = liability > 0 ? Math.min(100, (projected / liability) * 100) : 100;
  const color = onTrack ? "#3D7A5C" : "#B5482E";

  return (
    <div className="mb-6">
      <div className="flex justify-between items-baseline mb-2 font-sans">
        <span className="text-xs uppercase tracking-[0.12em] text-[#0F2A3D]/60 font-semibold">{label}</span>
        <span className="text-xs text-[#0F2A3D]/60">{Math.round(pct)}% of estimated liability</span>
      </div>
      <div className="relative h-3 bg-[#0F2A3D]/10 rounded-full overflow-hidden">
        <div
          className="h-full rounded-full transition-all duration-700"
          style={{ width: `${pct}%`, backgroundColor: color }}
        />
      </div>
      <div className="flex justify-between mt-2 font-serif text-sm">
        <span>Projected: <strong>{fmt(projected)}</strong></span>
        <span className="text-[#0F2A3D]/60">Estimated owed: {fmt(liability)}</span>
      </div>
    </div>
  );
}

function ResultsView({ results, stateName, isNYC, onBack }) {
  const {
    fedLiability, stateLiability, projectedFed, projectedState,
    fedGap, stateGap, fedRaisePerCheck, stateRaisePerCheck,
    periodsRemaining, fedOnTrack, stateOnTrack, hasStateTax, stateConfidence
  } = results;

  const overallOnTrack = fedOnTrack && (!hasStateTax || stateOnTrack);

  return (
    <div>
      <div
        className={`rounded-lg p-6 mb-8 ${overallOnTrack ? "bg-[#3D7A5C]/10 border border-[#3D7A5C]/30" : "bg-[#B5482E]/10 border border-[#B5482E]/30"}`}
      >
        <div className="font-sans text-xs uppercase tracking-[0.12em] font-bold mb-1"
             style={{ color: overallOnTrack ? "#3D7A5C" : "#B5482E" }}>
          {overallOnTrack ? "On track" : "Projected shortfall"}
        </div>
        <p className="font-serif text-lg leading-snug">
          {overallOnTrack
            ? "Based on your pace so far, your withholding is projected to meet or exceed what you'll owe by December 31."
            : "Based on your pace so far, your withholding is projected to fall short of what you'll owe by December 31."}
        </p>
      </div>

      <Gauge label="Federal" projected={projectedFed} liability={fedLiability} onTrack={fedOnTrack} />
      {hasStateTax && (
        <div>
          <Gauge
            label={`State${stateName === "New York" && isNYC ? " + NYC" : ` (${stateName})`}`}
            projected={projectedState}
            liability={stateLiability}
            onTrack={stateOnTrack}
          />
          {stateConfidence === "low" && (
            <p className="font-sans text-xs text-[#C99A3E] -mt-4 mb-6 leading-relaxed">
              {stateName} uses a rough estimated rate rather than real brackets, so treat this number as a wider-margin estimate.
            </p>
          )}
        </div>
      )}

      {(!fedOnTrack || (hasStateTax && !stateOnTrack)) && periodsRemaining > 0 && (
        <div className="mt-8 border-t border-[#0F2A3D]/15 pt-6">
          <h3 className="font-sans text-xs uppercase tracking-[0.12em] font-bold text-[#0F2A3D]/70 mb-3">
            What to adjust
          </h3>
          <div className="space-y-3 font-serif text-[15px] leading-relaxed">
            {!fedOnTrack && (
              <p>
                To close the federal gap of <strong>{fmt(fedGap)}</strong> over your remaining {periodsRemaining} pay period{periodsRemaining === 1 ? "" : "s"},
                increase extra federal withholding by about <strong>{fmt(fedRaisePerCheck)}</strong> per paycheck.
                This goes on line 4(c) of a new W-4 given to your employer.
              </p>
            )}
            {hasStateTax && !stateOnTrack && (
              <p>
                To close the state gap of <strong>{fmt(stateGap)}</strong>, increase state withholding by about <strong>{fmt(stateRaisePerCheck)}</strong> per paycheck,
                or ask your employer about your state's equivalent withholding adjustment form.
              </p>
            )}
          </div>
        </div>
      )}

      {periodsRemaining <= 0 && (!fedOnTrack || (hasStateTax && !stateOnTrack)) && (
        <div className="mt-8 border-t border-[#0F2A3D]/15 pt-6 font-serif text-[15px] leading-relaxed">
          <p>
            You've reached your last pay period for the year, so withholding adjustments won't have time to close the gap.
            An estimated quarterly payment directly to the IRS{hasStateTax ? " and your state" : ""} may be worth discussing before year end.
          </p>
        </div>
      )}

      <div className="mt-10 pt-6 border-t border-[#0F2A3D]/15">
        <p className="font-sans text-xs text-[#0F2A3D]/50 leading-relaxed">
          This is a simplified projection using standard deductions/exemptions and general bracket math for federal
          tax and for New York, New Jersey, and Texas. It doesn't account for itemized deductions, self-employment
          income, capital gains, or every credit, so treat it as a directional estimate. For a precise number, talk
          to a preparer.
        </p>
      </div>

      <div className="flex gap-3 mt-8">
        <button
          onClick={onBack}
          className="font-sans text-sm flex-1 border border-[#0F2A3D]/30 py-3 rounded hover:bg-[#0F2A3D]/5 transition-colors"
        >
          Start over
        </button>
        <a
          href="#"
          onClick={(e) => e.preventDefault()}
          className="font-sans text-sm flex-1 text-center bg-[#0F2A3D] text-[#F7F4EE] py-3 rounded hover:bg-[#0F2A3D]/90 transition-colors"
        >
          Talk to Sterling Tax Group
        </a>
      </div>
    </div>
  );
}
