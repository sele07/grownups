<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Comparative Quest</title>
    
    <!-- React and ReactDOM -->
    <script src="https://unpkg.com/react@18/umd/react.production.min.js" crossorigin></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js" crossorigin></script>
    
    <!-- Babel for in-browser JSX compilation -->
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-slate-50 overscroll-none">
    <div id="root"></div>

    <script type="text/babel">
        const { useState, useEffect, useRef } = React;

        // --- INLINE ICONS (Ensures no network requests are made offline) ---
        const User = (props) => <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" {...props}><path d="M19 21v-2a4 4 0 0 0-4-4H9a4 4 0 0 0-4 4v2"/><circle cx="12" cy="7" r="4"/></svg>;
        const Dices = (props) => <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" {...props}><rect width="12" height="12" x="2" y="10" rx="2" ry="2"/><path d="m17.92 14 3.5-3.5a2.24 2.24 0 0 0 0-3l-5-4.92a2.24 2.24 0 0 0-3 0L10 6"/><path d="M6 18h.01"/><path d="M10 14h.01"/><path d="M15 6h.01"/><path d="M18 9h.01"/></svg>;
        const ArrowRight = (props) => <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" {...props}><path d="M5 12h14"/><path d="m12 5 7 7-7 7"/></svg>;
        const Trophy = (props) => <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" {...props}><path d="M6 9H4.5a2.5 2.5 0 0 1 0-5H6"/><path d="M18 9h1.5a2.5 2.5 0 0 0 0-5H18"/><path d="M4 22h16"/><path d="M10 14.66V17c0 .55-.47.98-.97 1.21C7.85 18.75 7 20.24 7 22"/><path d="M14 14.66V17c0 .55.47.98.97 1.21C16.15 18.75 17 20.24 17 22"/><path d="M18 2H6v7a6 6 0 0 0 12 0V2Z"/></svg>;
        const RotateCcw = (props) => <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" {...props}><path d="M3 12a9 9 0 1 0 9-9 9.75 9.75 0 0 0-6.74 2.74L3 8"/><path d="M3 3v5h5"/></svg>;
        const Play = (props) => <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" {...props}><polygon points="6 3 20 12 6 21 6 3"/></svg>;
        const FileQuestion = (props) => <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" {...props}><path d="M14.5 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V7.5L14.5 2z"/><path d="M10 10.3c.2-.4.5-.8.9-1a2.1 2.1 0 0 1 2.6.4c.3.4.5.8.5 1.3 0 1.3-2 2-2 2"/><path d="M12 17h.01"/></svg>;
        const CheckCircle2 = (props) => <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" {...props}><circle cx="12" cy="12" r="10"/><path d="m9 12 2 2 4-4"/></svg>;
        const Lightbulb = (props) => <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" {...props}><path d="M15 14c.2-1 .7-1.7 1.5-2.5 1-.9 1.5-2.2 1.5-3.5A6 6 0 0 0 6 8c0 1.3.5 2.6 1.5 3.5.8.8 1.3 1.5 1.5 2.5"/><path d="M9 18h6"/><path d="M10 22h4"/></svg>;
        const AlertCircle = (props) => <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" {...props}><circle cx="12" cy="12" r="10"/><line x1="12" y1="8" x2="12" y2="12"/><line x1="12" y1="16" x2="12.01" y2="16"/></svg>;

        // --- GAME DATA ---
        const TILES = [
          { id: 0, text: "Start", type: "start" },
          { id: 1, text: "Who are ... (bad) drivers, men or women?", type: "question" },
          { id: 2, text: "Which is ... (difficult) to learn, maths or English?", type: "question" },
          { id: 3, text: "Which is ... (entertaining), watching films or playing computer games?", type: "question" },
          { id: 4, text: "Is it ... (good) to study English in class or by yourself?", type: "question" },
          { id: 5, text: "Does life get ... (complicated) the older you become?", type: "question" },
          { id: 6, text: "Is there anything ... (delicious) than your mother's cooking?", type: "question" },
          { id: 7, text: "Move forward one square", type: "action", action: 1 },
          { id: 8, text: "Are you ... (happy) now or when you were a child?", type: "question" },
          { id: 9, text: "When do you usually feel ... (hungry), before lunch or before dinner?", type: "question" },
          { id: 10, text: "Are some people ... (lucky) than others or do people make their own luck?", type: "question" },
          { id: 11, text: "Are you ... (smart) than your parents?", type: "question" },
          { id: 12, text: "Which room in your home is ... (clean), the kitchen or your bedroom?", type: "question" },
          { id: 13, text: "Are you ... (creative) now or when you were younger?", type: "question" },
          { id: 14, text: "Create your own comparative question!", type: "creative" },
          { id: 15, text: "Who are ... (friendly), the boys or girls in your class?", type: "question" },
          { id: 16, text: "Is the world ... (safe) now than it was 10 years ago?", type: "question" },
          { id: 17, text: "When were you ... (busy), yesterday or the day before yesterday?", type: "question" },
          { id: 18, text: "Is it ... (easy) to push or pull?", type: "question" },
          { id: 19, text: "Which building is ... (modern), your school or your house?", type: "question" },
          { id: 20, text: "Move forward two squares", type: "action", action: 2 },
          { id: 21, text: "Is it ... (important) to be kind or honest?", type: "question" },
          { id: 22, text: "Whose voice is ... (loud), yours or your best friend's?", type: "question" },
          { id: 23, text: "Which is ... (convenient), shopping online or shopping in a store?", type: "question" },
          { id: 24, text: "Is living in the country ... (cheap) than living in the city?", type: "question" },
          { id: 25, text: "Which is ... (safe), riding a bicycle or riding a motorbike?", type: "question" },
          { id: 26, text: "Is a mobile phone ... (useful) than a computer?", type: "question" },
          { id: 27, text: "Create your own comparative question!", type: "creative" },
          { id: 28, text: "Which pet is ... (popular), cats or dogs?", type: "question" },
          { id: 29, text: "Is it better to date someone who is ... (old) or ... (young) than you?", type: "question" },
          { id: 30, text: "Which is ... (healthy), eating good food or getting lots of exercise?", type: "question" },
          { id: 31, text: "Finish", type: "finish" }
        ];

        const VISUAL_ORDER = [
          31, 30, 29, 28,
          24, 25, 26, 27,
          23, 22, 21, 20,
          16, 17, 18, 19,
          15, 14, 13, 12,
           8,  9, 10, 11,
           7,  6,  5,  4,
           0,  1,  2,  3
        ];

        const Dice = ({ value, isRolling }) => {
          const dots = {
            1: ['col-start-2 row-start-2'],
            2: ['col-start-1 row-start-1', 'col-start-3 row-start-3'],
            3: ['col-start-1 row-start-1', 'col-start-2 row-start-2', 'col-start-3 row-start-3'],
            4: ['col-start-1 row-start-1', 'col-start-3 row-start-1', 'col-start-1 row-start-3', 'col-start-3 row-start-3'],
            5: ['col-start-1 row-start-1', 'col-start-3 row-start-1', 'col-start-2 row-start-2', 'col-start-1 row-start-3', 'col-start-3 row-start-3'],
            6: ['col-start-1 row-start-1', 'col-start-3 row-start-1', 'col-start-1 row-start-2', 'col-start-3 row-start-2', 'col-start-1 row-start-3', 'col-start-3 row-start-3']
          };

          return (
            <div className={`w-16 h-16 md:w-20 md:h-20 bg-white rounded-2xl border-2 border-b-[6px] border-[#e5e5e5] p-2 md:p-3 grid grid-cols-3 grid-rows-3 gap-1 ${isRolling ? 'animate-bounce' : ''} transform transition-transform duration-200`}>
              {dots[value]?.map((pos, i) => (
                <div key={i} className={`bg-[#4b4b4b] rounded-full w-full h-full ${pos}`} />
              ))}
            </div>
          );
        };

        function App() {
          const [players, setPlayers] = useState([
            { id: 1, name: "Player 1", pos: 0, color: 'bg-[#1cb0f6]', border: 'border-[#1899d6]', iconColor: 'text-[#1cb0f6]' },
            { id: 2, name: "Player 2", pos: 0, color: 'bg-[#ff4b4b]', border: 'border-[#ea1515]', iconColor: 'text-[#ff4b4b]' }
          ]);
          const [turn, setTurn] = useState(0);
          const [diceValue, setDiceValue] = useState(1);
          const [isRolling, setIsRolling] = useState(false);
          const [winner, setWinner] = useState(null);
          const [activeMessage, setActiveMessage] = useState("Roll the dice to start!");
          const [showCardModal, setShowCardModal] = useState(false);
          const [modalContent, setModalContent] = useState(null);

          const [answeredQuestions, setAnsweredQuestions] = useState([]);
          const answeredQuestionsRef = useRef([]);

          useEffect(() => {
            answeredQuestionsRef.current = answeredQuestions;
          }, [answeredQuestions]);

          const questionTileIds = TILES.filter(t => t.type === 'question' || t.type === 'creative').map(t => t.id);
          const unansweredCount = questionTileIds.length - answeredQuestions.length;

          const currentPlayer = players[turn];

          const wait = (ms) => new Promise(res => setTimeout(res, ms));

          const processLanding = (pos) => {
            const currentUnanswered = questionTileIds.filter(id => !answeredQuestionsRef.current.includes(id));

            if (pos === 31) {
                if (currentUnanswered.length > 0) {
                    const randomId = currentUnanswered[Math.floor(Math.random() * currentUnanswered.length)];
                    const qTile = TILES.find(t => t.id === randomId);
                    setModalContent({ ...qTile, isBonus: true, finishNotice: true });
                    setAnsweredQuestions(prev => [...prev, randomId]);
                    setTimeout(() => setShowCardModal(true), 300);
                } else {
                    setWinner({ ...players[turn], pos: 31 });
                    setActiveMessage(`${players[turn].name} WINS!`);
                }
                return;
            }

            const landedTile = TILES[pos];
            if (landedTile.type === 'question' || landedTile.type === 'creative') {
                if (answeredQuestionsRef.current.includes(pos)) {
                    if (currentUnanswered.length > 0) {
                        const randomId = currentUnanswered[Math.floor(Math.random() * currentUnanswered.length)];
                        const qTile = TILES.find(t => t.id === randomId);
                        setModalContent({ ...qTile, isBonus: true, originalPos: pos });
                        setAnsweredQuestions(prev => [...prev, randomId]);
                    } else {
                        setModalContent({ ...landedTile, freePass: true });
                    }
                } else {
                    setModalContent(landedTile);
                    setAnsweredQuestions(prev => [...prev, pos]);
                }
                setTimeout(() => setShowCardModal(true), 300);
            } else {
                setModalContent(landedTile);
                setTimeout(() => setShowCardModal(true), 300);
            }
          };

          const handleRoll = async () => {
            if (isRolling || winner || showCardModal) return;

            const currentUnanswered = questionTileIds.filter(id => !answeredQuestionsRef.current.includes(id));

            if (currentPlayer.pos === 31) {
                if (currentUnanswered.length > 0) {
                    const randomId = currentUnanswered[Math.floor(Math.random() * currentUnanswered.length)];
                    const qTile = TILES.find(t => t.id === randomId);
                    setModalContent({ ...qTile, isBonus: true, finishNotice: true });
                    setAnsweredQuestions(prev => [...prev, randomId]);
                    setShowCardModal(true);
                } else {
                    setWinner({ ...currentPlayer, pos: 31 });
                    setActiveMessage(`${currentPlayer.name} WINS!`);
                }
                return;
            }

            setIsRolling(true);
            setActiveMessage(`${currentPlayer.name} is rolling...`);
            
            let finalRoll = 1;
            for (let i = 0; i < 10; i++) {
              setDiceValue(Math.floor(Math.random() * 6) + 1);
              await wait(100);
            }
            finalRoll = Math.floor(Math.random() * 6) + 1;
            setDiceValue(finalRoll);
            setActiveMessage(`${currentPlayer.name} rolled a ${finalRoll}!`);
            await wait(500);

            let currentPos = currentPlayer.pos;
            for (let i = 0; i < finalRoll; i++) {
              currentPos++;
              if (currentPos >= 31) {
                currentPos = 31;
                break;
              }
              
              setPlayers(prevPlayers => {
                const updatedPlayers = [...prevPlayers];
                updatedPlayers[turn] = { ...updatedPlayers[turn], pos: currentPos };
                return updatedPlayers;
              });
              
              await wait(300);
            }

            processLanding(currentPos);
            setIsRolling(false);
          };

          const handleCloseModal = async () => {
            setShowCardModal(false);

            if (modalContent.type === 'action') {
              if (typeof modalContent.action === 'number') {
                let currentPos = players[turn].pos;
                for (let i = 0; i < modalContent.action; i++) {
                  currentPos++;
                  if (currentPos >= 31) currentPos = 31;
                  
                  setPlayers(prevPlayers => {
                    const newPlayers = [...prevPlayers];
                    newPlayers[turn] = { ...newPlayers[turn], pos: currentPos };
                    return newPlayers;
                  });
                  await wait(300);
                }

                processLanding(currentPos);
                return; 
              }
            }

            if (players[turn].pos === 31) {
                 const remaining = questionTileIds.filter(id => !answeredQuestionsRef.current.includes(id));
                 if (remaining.length === 0) {
                     setWinner({ ...players[turn], pos: 31 });
                     setActiveMessage(`${players[turn].name} WINS!`);
                     return;
                 }
            }

            setTurn(turn === 0 ? 1 : 0);
            setActiveMessage(`Ready for the next turn!`);
          };

          const resetGame = () => {
            setPlayers([
              { id: 1, name: "Player 1", pos: 0, color: 'bg-[#1cb0f6]', border: 'border-[#1899d6]', iconColor: 'text-[#1cb0f6]' },
              { id: 2, name: "Player 2", pos: 0, color: 'bg-[#ff4b4b]', border: 'border-[#ea1515]', iconColor: 'text-[#ff4b4b]' }
            ]);
            setTurn(0);
            setWinner(null);
            setDiceValue(1);
            setAnsweredQuestions([]);
            setActiveMessage("Game reset! Roll the dice.");
          };

          const formatTileText = (text, isModal = false) => {
            const parts = text.split(/(\.\.\.\s*\([^)]+\))/);
            return parts.map((part, i) => {
              if (part.match(/\.\.\.\s*\([^)]+\)/)) {
                return <span key={i} className={`font-extrabold ${isModal ? 'text-[#1cb0f6]' : 'text-[#afafaf]'}`}>{part}</span>;
              }
              return part;
            });
          };

          const getTileClasses = (tile, index) => {
            let base = "relative flex flex-col items-center justify-center text-center p-1.5 md:p-3 rounded-2xl border-2 border-b-[5px] transition-all duration-300 overflow-hidden min-h-[60px] md:min-h-[90px] font-bold ";
            
            if (tile.type === 'start') base += "bg-[#58cc02] border-[#58a700] text-white ";
            else if (tile.type === 'finish') base += "bg-[#ffc800] border-[#e5b400] text-white ";
            else if (tile.type === 'action') base += "bg-[#ff4b4b] border-[#ea1515] text-white ";
            else if (tile.type === 'creative') base += "bg-[#ce82ff] border-[#a560e8] text-white ";
            else base += "bg-white border-[#e5e5e5] text-[#4b4b4b] ";

            const activePlayers = players.filter(p => p.pos === tile.id);
            if (activePlayers.length > 0) {
              base += " transform scale-[1.05] z-10 ";
            }

            return base;
          };

          return (
            <div className="min-h-screen bg-slate-50 p-2 md:p-6 font-sans text-[#4b4b4b] flex flex-col">
              <header className="max-w-6xl mx-auto w-full flex flex-col sm:flex-row items-center justify-between bg-white p-4 rounded-2xl border-2 border-b-[4px] border-[#e5e5e5] mb-4 md:mb-6">
                <div className="flex items-center gap-3 mb-3 sm:mb-0">
                  <div className="p-2 bg-[#58cc02] rounded-xl border-b-[3px] border-[#58a700]">
                    <Dices className="text-white w-6 h-6 md:w-8 md:h-8" />
                  </div>
                  <div>
                    <h1 className="text-xl md:text-2xl font-extrabold tracking-wide text-[#4b4b4b]">Comparative Quest</h1>
                  </div>
                </div>

                <div className="hidden md:flex items-center gap-2 bg-slate-50 p-2 px-4 rounded-xl border-2 border-[#e5e5e5]">
                    <span className="text-[#afafaf] font-bold text-sm uppercase tracking-wider">Progress:</span>
                    <span className="text-[#1cb0f6] font-extrabold">{answeredQuestions.length} / {questionTileIds.length}</span>
                </div>

                <div className="flex items-center gap-4 bg-slate-50 p-2 px-4 rounded-xl border-2 border-[#e5e5e5]">
                  {players.map((p, i) => (
                    <div key={p.id} className={`flex items-center gap-2 ${turn === i ? 'opacity-100 font-extrabold scale-105' : 'opacity-40 grayscale'} transition-all duration-300`}>
                      <div className={`w-4 h-4 rounded-full ${p.color} border-2 border-b-[3px] ${p.border}`} />
                      <span className={`text-sm md:text-base ${turn === i ? 'text-[#4b4b4b]' : 'text-[#afafaf]'}`}>{p.name}</span>
                    </div>
                  ))}
                </div>
              </header>

              <div className="max-w-6xl mx-auto w-full flex flex-col lg:flex-row gap-4 md:gap-6 flex-1">
                <div className="lg:w-2/3 bg-transparent flex-1">
                  <div className="grid grid-cols-4 gap-2 md:gap-3 w-full h-full">
                    {VISUAL_ORDER.map((tileIndex) => {
                      const tile = TILES[tileIndex];
                      const occupants = players.filter(p => p.pos === tile.id);
                      const isAnswered = answeredQuestions.includes(tile.id);
                      
                      return (
                        <div key={tile.id} className={getTileClasses(tile, tileIndex)}>
                          <span className="absolute top-1 left-1 md:top-1.5 md:left-1.5 text-[8px] md:text-[10px] font-extrabold opacity-40 z-20">
                            {tile.id === 0 ? 'START' : tile.id === 31 ? 'END' : tile.id}
                          </span>
                          
                          <div className="w-full mt-2 md:mt-3 line-clamp-3 md:line-clamp-4 text-[9px] md:text-[11px] lg:text-xs font-bold leading-snug z-20">
                            {formatTileText(tile.text)}
                          </div>

                          {isAnswered && (tile.type === 'question' || tile.type === 'creative') && (
                            <div className="absolute inset-0 bg-white/70 z-10 flex items-center justify-center backdrop-blur-[0.5px]">
                                <CheckCircle2 className="w-8 h-8 md:w-12 md:h-12 text-[#58cc02] opacity-90 drop-shadow-sm" />
                            </div>
                          )}

                          {occupants.length > 0 && (
                            <div className="absolute inset-0 flex items-center justify-center gap-1 bg-white/30 backdrop-blur-[1px] rounded-lg z-30">
                              {occupants.map(p => (
                                <div key={p.id} className={`${p.color} w-7 h-7 md:w-9 md:h-9 rounded-full border-2 border-b-[4px] ${p.border} flex items-center justify-center transform transition-all duration-300 ${turn === p.id - 1 && isRolling ? 'animate-bounce scale-110' : ''}`}>
                                   <span className="text-white text-xs md:text-sm font-extrabold drop-shadow-sm">{p.id}</span>
                                </div>
                              ))}
                            </div>
                          )}
                        </div>
                      );
                    })}
                  </div>
                </div>

                <div className="lg:w-1/3 flex flex-col gap-4 md:gap-6 justify-center">
                  <div className="bg-white p-6 md:p-8 rounded-3xl border-2 border-b-[6px] border-[#e5e5e5] flex flex-col items-center gap-6 md:gap-10 h-full justify-center min-h-[300px]">
                    <div className="text-center space-y-2">
                       <p className="text-sm font-extrabold text-[#afafaf] uppercase tracking-widest">Game Status</p>
                       <p className="text-lg md:text-xl font-extrabold text-[#1cb0f6] h-8">
                         {activeMessage}
                       </p>
                    </div>

                    <div className="flex flex-col items-center gap-6 md:gap-8 w-full">
                      <Dice value={diceValue} isRolling={isRolling} />
                      
                      {!winner ? (
                        <button
                          onClick={handleRoll}
                          disabled={isRolling || showCardModal}
                          className={`w-full px-8 py-4 rounded-2xl font-extrabold text-lg uppercase tracking-widest transition-all flex items-center justify-center gap-3
                            ${isRolling || showCardModal
                              ? 'bg-[#e5e5e5] text-[#afafaf] border-2 border-b-4 border-[#cecece] cursor-not-allowed'
                              : currentPlayer.pos === 31 && unansweredCount === 0
                                ? 'bg-[#ffc800] text-white border-2 border-b-[4px] border-[#e5b400] hover:bg-[#e5b400]'
                                : 'bg-[#58cc02] text-white border-2 border-b-[4px] border-[#58a700] hover:bg-[#46a302] active:border-b-2 active:translate-y-[2px]'
                            }`}
                        >
                          {currentPlayer.pos === 31 && unansweredCount > 0 ? (
                            <><FileQuestion className="w-6 h-6" /> Draw Skipped</>
                          ) : currentPlayer.pos === 31 && unansweredCount === 0 ? (
                            <><Trophy className="w-6 h-6" /> Finish Game</>
                          ) : (
                            <><Play className="w-6 h-6 fill-current" /> Roll Dice</>
                          )}
                        </button>
                      ) : (
                        <button
                          onClick={resetGame}
                          className="w-full px-8 py-4 bg-[#1cb0f6] text-white border-2 border-b-[4px] border-[#1899d6] hover:bg-[#1495d4] active:border-b-2 active:translate-y-[2px] rounded-2xl font-extrabold text-lg uppercase tracking-widest transition-all flex items-center justify-center gap-3"
                        >
                          <RotateCcw className="w-6 h-6" /> Play Again
                        </button>
                      )}
                    </div>
                  </div>
                </div>
              </div>

              {showCardModal && modalContent && (
                <div className="fixed inset-0 bg-white flex flex-col z-50 animate-in fade-in duration-300">
                  <div className="flex-1 flex flex-col max-w-3xl mx-auto w-full p-6 md:p-10 mt-10">
                    <div className="flex items-center justify-between mb-10">
                      <span className="font-extrabold text-xl md:text-2xl text-[#afafaf] uppercase tracking-wider">
                        {modalContent.finishNotice ? "Finish Line Challenge" : modalContent.isBonus ? "Bonus Question" : `${currentPlayer.name}'s Turn`}
                      </span>
                      <span className="text-[#1cb0f6] text-sm md:text-base font-extrabold border-2 border-b-[4px] border-[#1cb0f6] px-4 py-2 rounded-xl">
                        Tile {modalContent.id}
                      </span>
                    </div>

                    <div className="flex-1 flex flex-col items-center justify-center text-center">
                      {modalContent.type === 'action' ? (
                        <AlertCircle className="w-24 h-24 text-[#ff4b4b] mb-8" />
                      ) : modalContent.type === 'creative' ? (
                        <div className={`w-24 h-24 bg-[#ce82ff] rounded-full border-2 border-b-[6px] border-[#a560e8] mb-8 flex items-center justify-center`}>
                           <Lightbulb className="w-12 h-12 text-white" />
                        </div>
                      ) : (
                        <div className={`w-24 h-24 ${currentPlayer.color} rounded-full border-2 border-b-[6px] ${currentPlayer.border} mb-8 flex items-center justify-center`}>
                           <User className="w-12 h-12 text-white" />
                        </div>
                      )}

                      {modalContent.finishNotice && (
                          <div className="mb-4 text-[#ffc800] font-extrabold text-lg md:text-xl uppercase tracking-widest">
                              You reached the end, but you must answer skipped questions!
                          </div>
                      )}
                      {modalContent.isBonus && !modalContent.finishNotice && (
                          <div className="mb-4 text-[#1cb0f6] font-extrabold text-lg md:text-xl uppercase tracking-widest">
                              Tile {modalContent.originalPos} was answered! Here's a skipped one.
                          </div>
                      )}
                      {modalContent.freePass && (
                          <div className="mb-4 text-[#58cc02] font-extrabold text-lg md:text-xl uppercase tracking-widest">
                              Tile answered and no questions left! Free pass!
                          </div>
                      )}
                      {modalContent.type === 'creative' && !modalContent.isBonus && !modalContent.finishNotice && (
                          <div className="mb-4 text-[#ce82ff] font-extrabold text-lg md:text-xl uppercase tracking-widest">
                              Time to get creative! Ask your partner a question.
                          </div>
                      )}
                      
                      <h2 className="text-3xl md:text-5xl lg:text-6xl font-extrabold leading-tight text-[#4b4b4b]">
                        {formatTileText(modalContent.text, true)}
                      </h2>
                    </div>
                  </div>

                  <div className="border-t-2 border-[#e5e5e5] p-6 md:p-8 bg-white">
                    <div className="max-w-3xl mx-auto flex justify-end">
                      <button
                        onClick={handleCloseModal}
                        className="w-full md:w-auto px-16 py-5 bg-[#58cc02] text-white border-2 border-b-[4px] border-[#58a700] hover:bg-[#46a302] active:border-b-2 active:translate-y-[2px] rounded-2xl font-extrabold text-xl uppercase tracking-widest transition-all flex items-center justify-center gap-3"
                      >
                        {modalContent.type === 'action' && typeof modalContent.action === 'number' 
                          ? 'Move Forward!' 
                          : modalContent.type === 'action' 
                            ? 'Okay' 
                            : 'Continue'}
                      </button>
                    </div>
                  </div>
                </div>
              )}

              {winner && (
                <div className="fixed inset-0 bg-white flex flex-col z-50 p-6 md:p-10 text-center animate-in fade-in duration-300">
                  <div className="flex-1 flex flex-col items-center justify-center max-w-2xl mx-auto w-full">
                    <Trophy className="w-32 h-32 md:w-40 md:h-40 text-[#ffc800] mb-8 drop-shadow-md" />
                    <h2 className="text-4xl md:text-6xl font-extrabold text-[#ffc800] mb-4 uppercase tracking-widest">Victory!</h2>
                    <p className="text-xl md:text-3xl text-[#4b4b4b] font-bold mb-12"><span className={`${winner.iconColor}`}>{winner.name}</span> has finished!</p>
                  </div>
                  
                  <div className="border-t-2 border-[#e5e5e5] p-6 md:p-8 -mx-6 md:-mx-10 mt-auto">
                    <div className="max-w-3xl mx-auto">
                      <button
                        onClick={resetGame}
                        className="w-full py-5 bg-[#1cb0f6] text-white border-2 border-b-[4px] border-[#1899d6] hover:bg-[#1495d4] active:border-b-2 active:translate-y-[2px] rounded-2xl font-extrabold text-xl uppercase tracking-widest transition-all"
                      >
                        Start New Game
                      </button>
                    </div>
                  </div>
                </div>
              )}
            </div>
          );
        }

        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<App />);
    </script>
</body>
</html>
