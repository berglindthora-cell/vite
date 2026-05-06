import { useState, useEffect } from "react";
import { Search } from "lucide-react";

export default function App() {
  const [completedDocs, setCompletedDocs] = useState({});
  const [searchTerm, setSearchTerm] = useState("");

  // Dæmi gögn (þú getur breytt þessu)
  const deliveryData = [
    { id: 1, street: "Laugavegur 10" },
    { id: 2, street: "Hverfisgata 22" },
    { id: 3, street: "Bankastræti 5" },
  ];

  // Vista í localStorage
  useEffect(() => {
    const saved = localStorage.getItem("utburdur-progress");
    if (saved) {
      setCompletedDocs(JSON.parse(saved));
    }
  }, []);

  useEffect(() => {
    localStorage.setItem(
      "utburdur-progress",
      JSON.stringify(completedDocs)
    );
  }, [completedDocs]);

  const toggleDone = (id) => {
    setCompletedDocs((prev) => ({
      ...prev,
      [id]: !prev[id],
    }));
  };

  const filtered = deliveryData.filter((item) =>
    item.street.toLowerCase().includes(searchTerm.toLowerCase())
  );

  const progressPercentage =
    (Object.keys(completedDocs).filter((k) => completedDocs[k]).length /
      deliveryData.length) *
    100;

  return (
    <div className="max-w-md mx-auto bg-slate-100 min-h-screen pb-32 font-sans select-none">
      
      {/* Header */}
      <div className="bg-indigo-700 text-white p-5 sticky top-0 z-20 shadow-md">
        <div className="flex justify-between items-end mb-2">
          <h1 className="text-xl font-bold tracking-tight">
            Útburður 101
          </h1>
          <span className="text-xs font-mono bg-indigo-900/50 px-2 py-1 rounded">
            {Math.round(progressPercentage)}% lokið
          </span>
        </div>

        {/* Progress bar */}
        <div className="w-full h-1.5 bg-indigo-900/30 rounded-full overflow-hidden mb-4">
          <div
            className="h-full bg-emerald-400 transition-all duration-500"
            style={{ width: `${progressPercentage}%` }}
          />
        </div>

        {/* Search */}
        <div className="relative">
          <Search className="absolute left-3 top-3 text-indigo-300 w-4 h-4" />
          <input
            type="text"
            placeholder="Leita að götu eða húsnúmeri..."
            className="w-full bg-indigo-800/50 border border-indigo-500/30 rounded-xl py-3 pl-10 pr-4 text-white placeholder-indigo-300 focus:outline-none focus:ring-2 focus:ring-emerald-400 text-sm transition-all"
            onChange={(e) => setSearchTerm(e.target.value)}
          />
        </div>
      </div>

      {/* Listi */}
      <div className="p-4 space-y-3">
        {filtered.map((item) => (
          <div
            key={item.id}
            onClick={() => toggleDone(item.id)}
            className={`p-4 rounded-xl shadow cursor-pointer transition ${
              completedDocs[item.id]
                ? "bg-emerald-200 line-through"
                : "bg-white"
            }`}
          >
            {item.street}
          </div>
        ))}
      </div>

      {/* Reset */}
      <button
        onClick={() => {
          if (window.confirm("Viltu hreinsa alla listann?"))
            setCompletedDocs({});
        }}
        className="m-4 text-xs text-slate-400 font-medium hover:text-red-500 transition-colors"
      >
        Hreinsa allar merkingar fyrir nýjan dag
      </button>
    </div>
  );
}
