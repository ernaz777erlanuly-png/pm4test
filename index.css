import { useEffect, useMemo, useState } from "react";
import { ALL_QUESTIONS, type OptionKey, type Question } from "./questionsData";

type Mode = "flash" | "practice" | "exam";

const MISTAKES_KEY = "exam-prep-mistakes";

const modeLabels: Record<Mode, string> = {
  flash: "Карточки",
  practice: "Тренажер",
  exam: "Рандом 30",
};

const optionButtonClass =
  "w-full rounded-xl border px-4 py-3 text-left transition duration-200 focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-indigo-500/60";

const shuffle = <T,>(items: T[]) => {
  const array = [...items];
  for (let i = array.length - 1; i > 0; i -= 1) {
    const j = Math.floor(Math.random() * (i + 1));
    [array[i], array[j]] = [array[j], array[i]];
  }
  return array;
};

const randomSample = <T,>(items: T[], count: number) => shuffle(items).slice(0, count);

const scoreFromAnswers = (questions: Question[], answers: Record<number, OptionKey>) =>
  questions.reduce((total, question) => {
    const userAnswer = answers[question.id];
    return total + (userAnswer === question.correctKey ? 1 : 0);
  }, 0);

export default function App() {
  const [mode, setMode] = useState<Mode>("flash");
  const [onlyWeak, setOnlyWeak] = useState(false);
  const [mistakes, setMistakes] = useState<number[]>([]);

  const [flashIndex, setFlashIndex] = useState(0);
  const [showFlashAnswer, setShowFlashAnswer] = useState(false);

  const [practiceIndex, setPracticeIndex] = useState(0);
  const [practiceAnswers, setPracticeAnswers] = useState<Record<number, OptionKey>>({});

  const [examQuestions, setExamQuestions] = useState<Question[]>([]);
  const [examIndex, setExamIndex] = useState(0);
  const [examAnswers, setExamAnswers] = useState<Record<number, OptionKey>>({});
  const [examFinished, setExamFinished] = useState(false);

  useEffect(() => {
    const raw = localStorage.getItem(MISTAKES_KEY);
    if (!raw) {
      return;
    }

    try {
      const parsed = JSON.parse(raw) as number[];
      if (Array.isArray(parsed)) {
        setMistakes(parsed.filter((item) => Number.isFinite(item)));
      }
    } catch {
      setMistakes([]);
    }
  }, []);

  useEffect(() => {
    localStorage.setItem(MISTAKES_KEY, JSON.stringify(mistakes));
  }, [mistakes]);

  const weakSet = useMemo(() => new Set(mistakes), [mistakes]);

  const sourceQuestions = useMemo(() => {
    if (!onlyWeak || weakSet.size === 0) {
      return ALL_QUESTIONS;
    }
    return ALL_QUESTIONS.filter((question) => weakSet.has(question.id));
  }, [onlyWeak, weakSet]);

  const flashQuestion = sourceQuestions[flashIndex] ?? sourceQuestions[0];
  const practiceQuestion = sourceQuestions[practiceIndex] ?? sourceQuestions[0];

  const practiceScore = useMemo(
    () => scoreFromAnswers(sourceQuestions, practiceAnswers),
    [sourceQuestions, practiceAnswers],
  );

  const updateMistakes = (question: Question, userAnswer: OptionKey) => {
    setMistakes((prev) => {
      const set = new Set(prev);
      if (userAnswer === question.correctKey) {
        set.delete(question.id);
      } else {
        set.add(question.id);
      }
      return [...set].sort((a, b) => a - b);
    });
  };

  const startExam = () => {
    const base = onlyWeak && sourceQuestions.length > 0 ? sourceQuestions : ALL_QUESTIONS;
    const selected = randomSample(base, Math.min(30, base.length));
    setExamQuestions(selected);
    setExamAnswers({});
    setExamIndex(0);
    setExamFinished(false);
  };

  useEffect(() => {
    if (mode === "exam" && examQuestions.length === 0) {
      startExam();
    }
  }, [mode, examQuestions.length]);

  useEffect(() => {
    setFlashIndex(0);
    setPracticeIndex(0);
    setShowFlashAnswer(false);
  }, [onlyWeak]);

  const examScore = scoreFromAnswers(examQuestions, examAnswers);
  const examWrong = examQuestions.filter((question) => examAnswers[question.id] !== question.correctKey);

  return (
    <main className="min-h-screen bg-slate-950 text-slate-100">
      <div className="mx-auto w-full max-w-5xl px-4 py-8 sm:px-6 lg:px-8">
        <header className="mb-8 space-y-3">
          <p className="text-sm font-medium uppercase tracking-[0.22em] text-indigo-300">Exam Trainer</p>
          <h1 className="text-3xl font-semibold tracking-tight text-white sm:text-4xl">Қолданбалы БҚ - 139 сұрақ</h1>
          <p className="max-w-3xl text-slate-300">
            Индивидуалды дайындық үшін 3 режим: карточка, тренажер және нақты емтихан форматына ұқсас рандомды 30
            сұрақ.
          </p>
        </header>

        <section className="mb-6 flex flex-wrap items-center gap-2">
          {(["flash", "practice", "exam"] as Mode[]).map((item) => (
            <button
              key={item}
              onClick={() => setMode(item)}
              className={`rounded-lg px-4 py-2 text-sm font-medium transition duration-200 ${
                mode === item
                  ? "bg-indigo-500 text-white shadow-lg shadow-indigo-500/30"
                  : "bg-white/5 text-slate-300 hover:bg-white/10"
              }`}
            >
              {modeLabels[item]}
            </button>
          ))}

          <label className="ml-auto flex cursor-pointer items-center gap-2 rounded-lg bg-white/5 px-3 py-2 text-sm text-slate-300 transition hover:bg-white/10">
            <input
              type="checkbox"
              checked={onlyWeak}
              onChange={(event) => setOnlyWeak(event.target.checked)}
              className="h-4 w-4 accent-indigo-500"
            />
            Тек қате сұрақтар ({mistakes.length})
          </label>
        </section>

        {sourceQuestions.length === 0 ? (
          <section className="rounded-2xl border border-white/10 bg-white/[0.03] p-6 text-slate-200">
            Қате сұрақтар тізімі бос. Алдымен тренажер не рандом 30 режимінде жұмыс істеп көріңіз.
          </section>
        ) : null}

        {mode === "flash" && flashQuestion ? (
          <section className="rounded-2xl border border-white/10 bg-white/[0.03] p-6 backdrop-blur-sm">
            <div className="mb-4 flex items-center justify-between text-sm text-slate-300">
              <span>
                Сұрақ {flashIndex + 1}/{sourceQuestions.length}
              </span>
              <span>Қиындар банкі: {mistakes.length}</span>
            </div>

            <div key={flashQuestion.id} className="animate-rise space-y-5">
              <h2 className="text-lg font-medium leading-relaxed text-white">
                {flashQuestion.id}. {flashQuestion.text}
              </h2>

              <div className="space-y-2">
                {flashQuestion.options.map((option) => {
                  const visible = showFlashAnswer && option.key === flashQuestion.correctKey;
                  return (
                    <div
                      key={option.key}
                      className={`rounded-xl border px-4 py-3 text-sm transition duration-300 ${
                        visible
                          ? "border-emerald-400/60 bg-emerald-500/15 text-emerald-100"
                          : "border-white/10 bg-white/[0.02] text-slate-300"
                      }`}
                    >
                      <span className="font-medium">{option.key})</span> {option.text || "-"}
                    </div>
                  );
                })}
              </div>
            </div>

            <div className="mt-6 flex flex-wrap gap-2">
              <button
                onClick={() => setShowFlashAnswer((prev) => !prev)}
                className="rounded-lg bg-indigo-500 px-4 py-2 text-sm font-medium text-white transition hover:bg-indigo-400"
              >
                {showFlashAnswer ? "Жасыру" : "Жауапты ашу"}
              </button>
              <button
                onClick={() => {
                  setFlashIndex((prev) => (prev === 0 ? sourceQuestions.length - 1 : prev - 1));
                  setShowFlashAnswer(false);
                }}
                className="rounded-lg bg-white/10 px-4 py-2 text-sm text-slate-200 transition hover:bg-white/20"
              >
                Артқа
              </button>
              <button
                onClick={() => {
                  setFlashIndex((prev) => (prev + 1) % sourceQuestions.length);
                  setShowFlashAnswer(false);
                }}
                className="rounded-lg bg-white/10 px-4 py-2 text-sm text-slate-200 transition hover:bg-white/20"
              >
                Келесі
              </button>
              <button
                onClick={() => {
                  setFlashIndex(Math.floor(Math.random() * sourceQuestions.length));
                  setShowFlashAnswer(false);
                }}
                className="rounded-lg bg-white/10 px-4 py-2 text-sm text-slate-200 transition hover:bg-white/20"
              >
                Рандом
              </button>
            </div>
          </section>
        ) : null}

        {mode === "practice" && practiceQuestion ? (
          <section className="rounded-2xl border border-white/10 bg-white/[0.03] p-6 backdrop-blur-sm">
            <div className="mb-4 flex items-center justify-between text-sm text-slate-300">
              <span>
                Сұрақ {practiceIndex + 1}/{sourceQuestions.length}
              </span>
              <span>
                Дұрыс: {practiceScore}/{sourceQuestions.length}
              </span>
            </div>

            <div className="mb-4 h-2 overflow-hidden rounded-full bg-white/10">
              <div
                className="h-full rounded-full bg-indigo-400 transition-all duration-500"
                style={{ width: `${((practiceIndex + 1) / sourceQuestions.length) * 100}%` }}
              />
            </div>

            <div key={practiceQuestion.id} className="animate-rise space-y-4">
              <h2 className="text-lg font-medium leading-relaxed text-white">
                {practiceQuestion.id}. {practiceQuestion.text}
              </h2>

              <div className="space-y-2">
                {practiceQuestion.options.map((option) => {
                  const selected = practiceAnswers[practiceQuestion.id] === option.key;
                  const isCorrect = option.key === practiceQuestion.correctKey;
                  const shouldShowResult = Boolean(practiceAnswers[practiceQuestion.id]);

                  let style = "border-white/10 bg-white/[0.02] text-slate-200 hover:bg-white/10";
                  if (shouldShowResult && isCorrect) {
                    style = "border-emerald-400/60 bg-emerald-500/15 text-emerald-100";
                  } else if (shouldShowResult && selected && !isCorrect) {
                    style = "border-rose-400/60 bg-rose-500/15 text-rose-100";
                  }

                  return (
                    <button
                      key={option.key}
                      onClick={() => {
                        setPracticeAnswers((prev) => ({ ...prev, [practiceQuestion.id]: option.key }));
                        updateMistakes(practiceQuestion, option.key);
                      }}
                      className={`${optionButtonClass} ${style}`}
                    >
                      <span className="font-medium">{option.key})</span> {option.text || "-"}
                    </button>
                  );
                })}
              </div>
            </div>

            <div className="mt-6 flex flex-wrap gap-2">
              <button
                onClick={() => setPracticeIndex((prev) => (prev === 0 ? sourceQuestions.length - 1 : prev - 1))}
                className="rounded-lg bg-white/10 px-4 py-2 text-sm text-slate-200 transition hover:bg-white/20"
              >
                Артқа
              </button>
              <button
                onClick={() => setPracticeIndex((prev) => (prev + 1) % sourceQuestions.length)}
                className="rounded-lg bg-indigo-500 px-4 py-2 text-sm font-medium text-white transition hover:bg-indigo-400"
              >
                Келесі
              </button>
              <button
                onClick={() => {
                  setPracticeAnswers({});
                  setPracticeIndex(0);
                }}
                className="rounded-lg bg-white/10 px-4 py-2 text-sm text-slate-200 transition hover:bg-white/20"
              >
                Нөлден бастау
              </button>
            </div>
          </section>
        ) : null}

        {mode === "exam" ? (
          <section className="rounded-2xl border border-white/10 bg-white/[0.03] p-6 backdrop-blur-sm">
            {examQuestions.length === 0 ? null : (
              <>
                <div className="mb-4 flex items-center justify-between text-sm text-slate-300">
                  <span>
                    Сұрақ {examIndex + 1}/{examQuestions.length}
                  </span>
                  <span>Формат: рандом тест (30)</span>
                </div>

                <div className="mb-4 h-2 overflow-hidden rounded-full bg-white/10">
                  <div
                    className="h-full rounded-full bg-indigo-400 transition-all duration-500"
                    style={{ width: `${(Object.keys(examAnswers).length / examQuestions.length) * 100}%` }}
                  />
                </div>

                {!examFinished ? (
                  <div key={examQuestions[examIndex].id} className="animate-rise space-y-4">
                    <h2 className="text-lg font-medium leading-relaxed text-white">
                      {examQuestions[examIndex].id}. {examQuestions[examIndex].text}
                    </h2>

                    <div className="space-y-2">
                      {examQuestions[examIndex].options.map((option) => {
                        const selected = examAnswers[examQuestions[examIndex].id] === option.key;
                        return (
                          <button
                            key={option.key}
                            onClick={() =>
                              setExamAnswers((prev) => ({
                                ...prev,
                                [examQuestions[examIndex].id]: option.key,
                              }))
                            }
                            className={`${optionButtonClass} ${
                              selected
                                ? "border-indigo-400/70 bg-indigo-500/20 text-indigo-100"
                                : "border-white/10 bg-white/[0.02] text-slate-200 hover:bg-white/10"
                            }`}
                          >
                            <span className="font-medium">{option.key})</span> {option.text || "-"}
                          </button>
                        );
                      })}
                    </div>

                    <div className="mt-6 flex flex-wrap gap-2">
                      <button
                        onClick={() =>
                          setExamIndex((prev) => (prev === 0 ? examQuestions.length - 1 : prev - 1))
                        }
                        className="rounded-lg bg-white/10 px-4 py-2 text-sm text-slate-200 transition hover:bg-white/20"
                      >
                        Артқа
                      </button>
                      <button
                        onClick={() => setExamIndex((prev) => (prev + 1) % examQuestions.length)}
                        className="rounded-lg bg-white/10 px-4 py-2 text-sm text-slate-200 transition hover:bg-white/20"
                      >
                        Келесі
                      </button>
                      <button
                        onClick={() => {
                          setExamFinished(true);
                          examQuestions.forEach((question) => {
                            const answer = examAnswers[question.id];
                            if (answer) {
                              updateMistakes(question, answer);
                            }
                          });
                        }}
                        className="rounded-lg bg-indigo-500 px-4 py-2 text-sm font-medium text-white transition hover:bg-indigo-400"
                      >
                        Аяқтау
                      </button>
                      <button
                        onClick={startExam}
                        className="rounded-lg bg-white/10 px-4 py-2 text-sm text-slate-200 transition hover:bg-white/20"
                      >
                        Жаңадан 30 сұрақ
                      </button>
                    </div>
                  </div>
                ) : (
                  <div className="space-y-4">
                    <h2 className="text-2xl font-semibold text-white">
                      Нәтиже: {examScore}/{examQuestions.length}
                    </h2>
                    <p className="text-slate-300">
                      Пайыз: {Math.round((examScore / examQuestions.length) * 100)}%. Қате сұрақтар автоматты түрде "Тек
                      қате сұрақтар" банкіне қосылды.
                    </p>
                    <button
                      onClick={startExam}
                      className="rounded-lg bg-indigo-500 px-4 py-2 text-sm font-medium text-white transition hover:bg-indigo-400"
                    >
                      Қайта тапсыру
                    </button>

                    {examWrong.length > 0 ? (
                      <div className="mt-3 space-y-2 border-t border-white/10 pt-4">
                        <h3 className="text-sm font-medium uppercase tracking-wide text-slate-300">Қате кеткен сұрақтар</h3>
                        {examWrong.slice(0, 8).map((question) => (
                          <p key={question.id} className="text-sm text-slate-300">
                            {question.id}. {question.text}
                          </p>
                        ))}
                        {examWrong.length > 8 ? (
                          <p className="text-sm text-slate-400">Тағы {examWrong.length - 8} сұрақ бар...</p>
                        ) : null}
                      </div>
                    ) : null}
                  </div>
                )}
              </>
            )}
          </section>
        ) : null}
      </div>
    </main>
  );
}
