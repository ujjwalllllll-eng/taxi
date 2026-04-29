import { useState, useEffect } from 'react';
import { JourneyForm, JourneyData } from './components/JourneyForm';
import { JourneyList } from './components/JourneyList';
import { Statistics } from './components/Statistics';
import { Car, Download } from 'lucide-react';
import { toast } from 'sonner';
import { Toaster } from 'sonner';

export default function App() {
  const [journeys, setJourneys] = useState<JourneyData[]>([]);

  useEffect(() => {
    const saved = localStorage.getItem('driverJourneys');
    if (saved) {
      setJourneys(JSON.parse(saved));
    }
  }, []);

  useEffect(() => {
    localStorage.setItem('driverJourneys', JSON.stringify(journeys));
  }, [journeys]);

  const handleAddJourney = (journey: JourneyData) => {
    setJourneys([journey, ...journeys]);
    toast.success('Journey added successfully!');
  };

  const handleDeleteJourney = (id: string) => {
    if (confirm('Are you sure you want to delete this journey?')) {
      setJourneys(journeys.filter(j => j.id !== id));
      toast.success('Journey deleted successfully!');
    }
  };

  const handleExport = () => {
    const dataStr = JSON.stringify(journeys, null, 2);
    const dataBlob = new Blob([dataStr], { type: 'application/json' });
    const url = URL.createObjectURL(dataBlob);
    const link = document.createElement('a');
    link.href = url;
    link.download = `journeys-${new Date().toISOString().split('T')[0]}.json`;
    link.click();
    URL.revokeObjectURL(url);
    toast.success('Data exported successfully!');
  };

  return (
    <div className="min-h-screen bg-gray-50">
      <Toaster position="top-right" richColors />

      {/* Header */}
      <header className="bg-gradient-to-r from-blue-600 to-blue-700 text-white shadow-lg">
        <div className="container mx-auto px-4 py-6">
          <div className="flex items-center justify-between">
            <div className="flex items-center gap-3">
              <div className="bg-white/20 p-2 rounded-lg">
                <Car className="w-8 h-8" />
              </div>
              <div>
                <h1 className="font-semibold">Driver Journey Tracker</h1>
                <p className="text-sm text-blue-100">Track vehicles, drivers, and calculate distances automatically</p>
              </div>
            </div>
            {journeys.length > 0 && (
              <button
                onClick={handleExport}
                className="px-4 py-2 bg-white/20 hover:bg-white/30 rounded-md flex items-center gap-2 transition-colors"
              >
                <Download className="w-4 h-4" />
                Export Data
              </button>
            )}
          </div>
        </div>
      </header>

      {/* Main Content */}
      <main className="container mx-auto px-4 py-8">
        <Statistics journeys={journeys} />

        <div className="space-y-8">
          <JourneyForm onSubmit={handleAddJourney} />
          <JourneyList journeys={journeys} onDelete={handleDeleteJourney} />
        </div>
      </main>

      {/* Footer */}
      <footer className="bg-white border-t mt-12 py-6">
        <div className="container mx-auto px-4 text-center text-sm text-gray-600">
          <p>Driver Journey Tracker © 2026 | All journey data is stored locally in your browser</p>
        </div>
      </footer>
    </div>
  );
}
