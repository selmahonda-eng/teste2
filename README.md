# teste2
Calendario
import { useState } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Input } from "@/components/ui/input";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";

const exams = [
  { date: "2025-10-04", exam: "B2 First (FCE)", state: "SP", location: "Pinheiros (sede)", deadline: "2025-08-26" },
  { date: "2025-10-18", exam: "C1 Advanced (CAE)", state: "SP", location: "Pinheiros (sede)", deadline: "2025-09-09" },
  { date: "2025-11-25", exam: "B2 First (FCE)", state: "SP", location: "Vila Mariana", deadline: "2025-10-17" },
  { date: "2025-11-25", exam: "B2 First (FCE)", state: "SP", location: "Tatuapé", deadline: "2025-10-17" },
  { date: "2025-11-25", exam: "B2 First (FCE)", state: "SP", location: "Higienópolis", deadline: "2025-10-17" },
  { date: "2025-11-26", exam: "C1 Advanced (CAE)", state: "SP", location: "Vila Mariana", deadline: "2025-10-20" },
  { date: "2025-11-26", exam: "C1 Advanced (CAE)", state: "SP", location: "Tatuapé", deadline: "2025-10-20" },
  { date: "2025-11-26", exam: "C1 Advanced (CAE)", state: "SP", location: "Higienópolis", deadline: "2025-10-20" },
  { date: "2025-12-04", exam: "B1 Preliminary (PET)", state: "SP", location: "Pinheiros (sede)", deadline: "2025-10-28" },
  { date: "2025-12-04", exam: "B1 Preliminary (PET)", state: "SP", location: "Vila Mariana", deadline: "2025-10-28" },
  { date: "2025-12-04", exam: "B1 Preliminary (PET)", state: "SP", location: "Tatuapé", deadline: "2025-10-28" },
  { date: "2025-12-04", exam: "B1 Preliminary (PET)", state: "SP", location: "Higienópolis", deadline: "2025-10-28" }
];

export default function ExamAgenda() {
  const [search, setSearch] = useState("");
  const [filterExam, setFilterExam] = useState("all");
  const [filterState, setFilterState] = useState("all");
  const [startDate, setStartDate] = useState("");
  const [endDate, setEndDate] = useState("");

  const filteredExams = exams.filter((e) => {
    const matchesSearch = e.exam.toLowerCase().includes(search.toLowerCase()) || e.location.toLowerCase().includes(search.toLowerCase());
    const matchesExam = filterExam === "all" || e.exam === filterExam;
    const matchesState = filterState === "all" || e.state === filterState;
    const examDate = new Date(e.date);
    const matchesStartDate = !startDate || examDate >= new Date(startDate);
    const matchesEndDate = !endDate || examDate <= new Date(endDate);
    return matchesSearch && matchesExam && matchesState && matchesStartDate && matchesEndDate;
  });

  const exportToCSV = () => {
    const headers = ["Data", "Exame", "UF", "Local", "Inscrição até"];
    const rows = filteredExams.map(e => [e.date, e.exam, e.state, e.location, e.deadline]);
    const csvContent = [headers, ...rows].map(r => r.join(",")).join("\n");
    const blob = new Blob([csvContent], { type: "text/csv;charset=utf-8;" });
    const url = URL.createObjectURL(blob);
    const link = document.createElement("a");
    link.href = url;
    link.download = "agenda-exames.csv";
    link.click();
  };

  return (
    <div className="p-6 max-w-6xl mx-auto">
      <h1 className="text-2xl font-bold mb-4">Agenda de Exames Cambridge 2025</h1>

      <div className="flex flex-wrap gap-4 mb-6 items-center">
        <Input
          placeholder="Buscar por exame ou local..."
          value={search}
          onChange={(e) => setSearch(e.target.value)}
        />

        <Select onValueChange={setFilterExam} defaultValue="all">
          <SelectTrigger className="w-[200px]">
            <SelectValue placeholder="Filtrar por exame" />
          </SelectTrigger>
          <SelectContent>
            <SelectItem value="all">Todos</SelectItem>
            {Array.from(new Set(exams.map((e) => e.exam))).map((exam) => (
              <SelectItem key={exam} value={exam}>{exam}</SelectItem>
            ))}
          </SelectContent>
        </Select>

        <Select onValueChange={setFilterState} defaultValue="all">
          <SelectTrigger className="w-[200px]">
            <SelectValue placeholder="Filtrar por UF" />
          </SelectTrigger>
          <SelectContent>
            <SelectItem value="all">Todas</SelectItem>
            {Array.from(new Set(exams.map((e) => e.state))).map((uf) => (
              <SelectItem key={uf} value={uf}>{uf}</SelectItem>
            ))}
          </SelectContent>
        </Select>

        <div className="flex gap-2 items-center">
          <label className="text-sm">De:</label>
          <Input type="date" value={startDate} onChange={(e) => setStartDate(e.target.value)} className="w-[160px]" />
          <label className="text-sm">Até:</label>
          <Input type="date" value={endDate} onChange={(e) => setEndDate(e.target.value)} className="w-[160px]" />
        </div>

        <Button onClick={exportToCSV} className="ml-auto">Exportar CSV</Button>
      </div>

      <div className="grid md:grid-cols-2 lg:grid-cols-3 gap-6">
        {filteredExams.map((exam, idx) => (
          <motion.div key={idx} whileHover={{ scale: 1.03 }}>
            <Card className="shadow-lg rounded-2xl">
              <CardContent className="p-4">
                <h2 className="text-lg font-semibold">{exam.exam}</h2>
                <p><strong>Data:</strong> {new Date(exam.date).toLocaleDateString("pt-BR", { weekday: 'long', day: '2-digit', month: '2-digit', year: 'numeric' })}</p>
                <p><strong>Local:</strong> {exam.location} - {exam.state}</p>
                <p><strong>Inscrição até:</strong> {new Date(exam.deadline).toLocaleDateString("pt-BR")}</p>
              </CardContent>
            </Card>
          </motion.div>
        ))}
      </div>
    </div>
  );
}
