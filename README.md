<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Skill Dash Game (Final Fixes)</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.2.0/css/all.min.css">

    <style>
        /* CSS remains the same as the previous 'natural drop fixed' version */
        body { display: flex; justify-content: center; align-items: center; min-height: 100vh; background: linear-gradient(135deg, #e0f7fa 0%, #b2ebf2 100%); margin: 0; font-family: 'Poppins', sans-serif; }
        #game-container { width: 480px; height: 650px; background-color: #ffffff; border: none; position: relative; overflow: hidden; box-shadow: 0 8px 25px rgba(0, 0, 0, 0.15); border-radius: 15px; display: flex; flex-direction: column; }
        #game-area { flex-grow: 1; position: relative; padding: 10px; background: linear-gradient(to bottom, #a1c4fd, #c2e9fb); background-size: cover; background-position: center; background-repeat: no-repeat; overflow: hidden; }
        #controls { padding: 15px; background-color: #f1f8e9; border-top: 2px solid #c8e6c9; display: flex; justify-content: center; align-items: center; gap: 10px; position: relative; z-index: 5; }
        #answer-input { padding: 12px; font-size: 1.2em; width: 60%; border: 2px solid #aed581; border-radius: 6px; text-align: center; }
        #answer-input:focus { outline: none; border-color: #7cb342; box-shadow: 0 0 5px rgba(124, 179, 66, 0.5); }
        #submit-btn { padding: 12px 25px; font-size: 1.1em; font-weight: 600; cursor: pointer; border: none; background: linear-gradient(45deg, #4caf50, #81c784); color: white; border-radius: 6px; transition: background 0.3s ease, transform 0.1s ease; box-shadow: 0 2px 4px rgba(0,0,0,0.2); }
        #submit-btn:hover { background: linear-gradient(45deg, #388e3c, #66bb6a); }
        #submit-btn:active { transform: scale(0.98); }
        #top-bar { display: flex; justify-content: space-between; align-items: center; padding: 10px 15px; background-color: #00796b; color: white; border-bottom: 3px solid #004d40; z-index: 5; position: relative; }
        .top-info { display: flex; align-items: center; gap: 15px; font-size: 1.0em; font-weight: 600; }
        #user-id-display { font-size: 0.8em; opacity: 0.8; background-color: rgba(255,255,255,0.1); padding: 2px 6px; border-radius: 4px; }
        #lives-board { margin-left: auto; }
        #exit-button { background: rgba(255, 255, 255, 0.1); border: none; color: white; font-size: 1.1em; cursor: pointer; padding: 6px 10px; border-radius: 50%; transition: background-color 0.2s ease, color 0.2s ease; line-height: 1; margin-left: 10px; }
        #exit-button:hover { background-color: rgba(255, 82, 82, 0.8); color: #fff; }
        #exit-button i { display: block; }
        #mode-selector { display: flex; justify-content: center; gap: 10px; padding: 10px; background-color: #e0f2f1; border-bottom: 1px solid #b2dfdb; }
        .mode-button { padding: 8px 15px; font-size: 0.9em; font-weight: 600; cursor: pointer; border: 1px solid #00796b; border-radius: 20px; background-color: white; color: #00796b; transition: all 0.2s ease; }
        .mode-button:hover { background-color: #b2dfdb; }
        .mode-button.active { background-color: #00796b; color: white; }
        .falling-element { position: absolute; padding: 10px 18px; background-color: rgba(255, 255, 255, 0.9); border: 1px solid rgba(0,0,0,0.1); border-radius: 8px; font-size: 1.4em; font-weight: 600; color: #333; white-space: nowrap; top: -60px; left: 100px; box-shadow: 0 3px 6px rgba(0, 0, 0, 0.1); z-index: 2; opacity: 0; transform: translateY(-20px) scale(0.9); transition: top 0.1s linear, opacity 0.3s ease-out, transform 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275), background-color 0.3s ease; }
        .falling-element.visible { opacity: 1; transform: translateY(0) scale(1); }
        .falling-element.math { color: #8c450b; background-color: rgba(255, 204, 128, 0.9); }
        .falling-element.word { color: #1a237e; background-color: rgba(225, 245, 254, 0.9); font-weight: 400; font-size: 1.3em; }
        .overlay-screen { position: absolute; top: 0; left: 0; width: 100%; height: 100%; background-color: rgba(0, 121, 107, 0.95); z-index: 90; display: flex; flex-direction: column; justify-content: center; align-items: center; color: white; text-align: center; padding: 20px; box-sizing: border-box; opacity: 1; transition: opacity 0.5s ease, visibility 0.5s ease; visibility: visible; }
        .overlay-screen.hidden { opacity: 0; visibility: hidden; pointer-events: none; }
        #start-screen h2 { font-size: 2.5em; margin-bottom: 20px; font-weight: 700; text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3); }
        #start-screen p { font-size: 1.1em; margin-bottom: 30px; max-width: 80%; }
        #initial-start-button { padding: 15px 40px; font-size: 1.4em; font-weight: 600; cursor: pointer; border: none; background: linear-gradient(45deg, #ffca28, #ffa000); color: #424242; border-radius: 8px; transition: background 0.3s ease, transform 0.1s ease; box-shadow: 0 4px 8px rgba(0,0,0,0.2); margin-bottom: 20px; }
        #initial-start-button:hover { background: linear-gradient(45deg, #ffb300, #ff8f00); }
        #initial-start-button:active { transform: translateY(2px) scale(0.98); }
        #show-leaderboard-button { background: none; border: 1px solid white; color: white; padding: 8px 15px; border-radius: 5px; cursor: pointer; transition: background-color 0.2s ease; font-size: 0.9em; }
        #show-leaderboard-button:hover { background-color: rgba(255,255,255,0.2); }
        #leaderboard-screen { background-color: rgba(0, 80, 70, 0.98); justify-content: flex-start; padding-top: 30px; }
        #leaderboard-screen h2 { font-size: 2em; margin-bottom: 25px; }
        #leaderboard-logo { font-size: 3em; margin-bottom: 15px; color: #ffca28; }
        #leaderboard-list { list-style: none; padding: 0; margin: 0; width: 80%; max-width: 300px; max-height: 60%; overflow-y: auto; }
        #leaderboard-list li { background-color: rgba(255, 255, 255, 0.1); margin-bottom: 8px; padding: 10px 15px; border-radius: 5px; display: flex; justify-content: space-between; font-size: 1em; }
        #leaderboard-list li .rank { font-weight: 600; margin-right: 10px; min-width: 20px; text-align: right;}
        #leaderboard-list li .id { opacity: 0.9; flex-grow: 1; text-align: left; margin-left: 10px; overflow: hidden; text-overflow: ellipsis; white-space: nowrap;}
        #leaderboard-list li .score { font-weight: 600; }
        #close-leaderboard-button { margin-top: 20px; background-color: #ff7043; color: white; border: none; padding: 10px 20px; border-radius: 5px; cursor: pointer; font-size: 1em; transition: background-color 0.2s ease; }
        #close-leaderboard-button:hover { background-color: #f4511e; }
        #message-board { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); font-size: 1.8em; font-weight: 600; color: #d32f2f; background-color: rgba(255, 255, 255, 0.95); padding: 30px 40px; border-radius: 10px; border: 3px solid #d32f2f; text-align: center; z-index: 100; box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2); display: none; }
        #message-board button { margin-top: 20px; padding: 12px 25px; font-size: 0.8em; font-weight: 600; cursor: pointer; border: none; background-color: #1976d2; color: white; border-radius: 5px; }
        #message-board button:hover { background-color: #1565c0; }
        @keyframes shake { 0% { transform: translateX(0); } 25% { transform: translateX(-5px); } 50% { transform: translateX(5px); } 75% { transform: translateX(-5px); } 100% { transform: translateX(0); } }
        .shake { animation: shake 0.3s ease-in-out; }

    </style>
</head>
<body>

    <div id="game-container">
        <div id="start-screen" class="overlay-screen">
             <h2>Skill Dash!</h2>
             <p>Test your Math and Typing skills!</p>
             <button id="initial-start-button">Start Game</button>
             <button id="show-leaderboard-button">View Leaderboard</button>
        </div>
        <div id="leaderboard-screen" class="overlay-screen hidden">
            <div id="leaderboard-logo"><i class="fas fa-trophy"></i></div>
            <h2>Leaderboard</h2>
            <ul id="leaderboard-list">
                 <li><span class="rank">1.</span> <span class="id">XYZ789</span> <span class="score">150</span></li>
                 <li><span class="rank">2.</span> <span class="id">ABC123</span> <span class="score">135</span></li>
                 <li><span class="rank">3.</span> <span class="id">PQR456</span> <span class="score">120</span></li>
                 <li><span class="rank">4.</span> <span class="id">JKL012</span> <span class="score">110</span></li>
                 <li><span class="rank">5.</span> <span class="id">MNO345</span> <span class="score">95</span></li>
            </ul>
            <button id="close-leaderboard-button">Close</button>
        </div>
        <div id="top-bar" style="visibility: hidden;">
             <div class="top-info">
                 <div id="score-board">Score: 0</div>
                 <div id="user-id-display">ID: GUEST</div>
            </div>
             <div id="lives-board">Lives: ❤️❤️❤️</div>
             <button id="exit-button" title="Exit to Menu"><i class="fas fa-times"></i></button>
        </div>
        <div id="mode-selector" style="visibility: hidden;">
            <button id="mode-math-button" class="mode-button active">Math</button>
            <button id="mode-typing-button" class="mode-button">Typing</button>
        </div>
        <div id="game-area"></div>
         <div id="message-board" style="display: none;">
            Game Over! <br>
            <span id="final-score"></span> <br>
            <button onclick="showStartScreen()">Play Again</button>
        </div>
        <div id="controls" style="visibility: hidden;">
            <input type="text" id="answer-input" placeholder="Type here...">
            <button id="submit-btn">Enter</button>
        </div>
    </div>

    <audio id="correct-sound" src="correct.mp3" preload="auto"></audio>
    <audio id="wrong-sound" src="wrong.mp3" preload="auto"></audio>

    <script>
        // --- Get Elements ---
        const gameContainer = document.getElementById('game-container');
        const gameArea = document.getElementById('game-area');
        const scoreBoard = document.getElementById('score-board');
        const livesBoard = document.getElementById('lives-board');
        const answerInput = document.getElementById('answer-input');
        const submitButton = document.getElementById('submit-btn');
        const messageBoard = document.getElementById('message-board');
        const finalScoreDisplay = document.getElementById('final-score');
        const controlsDiv = document.getElementById('controls');
        const topBar = document.getElementById('top-bar');
        const startScreen = document.getElementById('start-screen');
        const initialStartButton = document.getElementById('initial-start-button');
        const correctSound = document.getElementById('correct-sound');
        const wrongSound = document.getElementById('wrong-sound');
        const userIdDisplay = document.getElementById('user-id-display');
        const exitButton = document.getElementById('exit-button');
        const modeSelector = document.getElementById('mode-selector');
        const modeMathButton = document.getElementById('mode-math-button');
        const modeTypingButton = document.getElementById('mode-typing-button');
        const leaderboardScreen = document.getElementById('leaderboard-screen');
        const showLeaderboardButton = document.getElementById('show-leaderboard-button');
        const closeLeaderboardButton = document.getElementById('close-leaderboard-button');

        // --- Game Variables ---
        let containerHeight = 0;
        let containerWidth = 0;
        const baseFallSpeed = 1.5;
        const speedIncrement = 0.1;
        const scoreIntervalForSpeedIncrease = 3;
        const maxFallSpeed = 6;
        let currentElement = null;
        let currentCorrectAnswer = null;
        let score = 0;
        let lives = 3;
        let gameInterval = null;
        let fallSpeed = baseFallSpeed;
        let isGameOver = false;
        let operationTypes = ['add'];
        let currentGameMode = 'math';
        let audioUnlocked = false;
        let currentUserID = "GUEST";

        // --- EXPANDED WORD LIST ---
        const wordList = [
            "about", "above", "across", "action", "active", "activity", "actor", "added", "address", "adult",
            "advice", "afraid", "after", "again", "against", "agent", "agree", "ahead", "alike", "alive",
            "allow", "almost", "alone", "along", "aloud", "already", "although", "amount", "angle", "animal",
            "another", "answer", "apart", "apple", "apply", "around", "arrive", "article", "aside", "asked",
            "attack", "author", "avoid", "aware", "away", "baby", "back", "ball", "band", "bank", "base",
            "basic", "basis", "basket", "battle", "beach", "beans", "bear", "beat", "beauty", "became", "because",
            "become", "been", "before", "began", "begin", "behind", "being", "believe", "belong", "below", "beside",
            "between", "beyond", "bicycle", "bigger", "birth", "black", "blank", "block", "blood", "blue", "board",
            "boat", "body", "bone", "book", "born", "borrow", "both", "bottle", "bottom", "bound", "bowl", "branch",
            "brave", "bread", "break", "breath", "breathe", "bridge", "brief", "bright", "bring", "broad", "broke",
            "broken", "brother", "brought", "brown", "build", "built", "burst", "bush", "busy", "butter", "cabin",
            "cage", "cake", "call", "calm", "came", "camp", "canal", "cannot", "captain", "card", "care", "careful",
            "carried", "carry", "case", "castle", "catch", "cattle", "caught", "cause", "cave", "cell", "cent", "center",
            "central", "century", "certain", "chair", "chance", "change", "chapter", "character", "charge", "chart", "check",
            "cheese", "chief", "child", "children", "choice", "choose", "chose", "church", "circle", "circus", "citizen",
            "city", "class", "classroom", "claws", "clay", "clean", "clear", "climb", "clock", "close", "closely", "closer",
            "cloth", "clothes", "clothing", "cloud", "club", "coach", "coal", "coast", "coat", "coffee", "cold", "collect",
            "college", "colony", "color", "column", "combination", "combine", "come", "comfortable", "coming", "command", "common",
            "community", "company", "compare", "compass", "complete", "completely", "complex", "composed", "composition", "compound", "concerned",
            "condition", "congress", "connected", "consider", "consist", "consonant", "constantly", "construction", "contain", "continent", "continued",
            "contrast", "control", "conversation", "cook", "cookies", "cool", "copper", "copy", "corn", "corner", "correct", "cost",
            "cotton", "could", "count", "country", "couple", "courage", "course", "court", "cover", "crew", "crop", "cross",
            "crowd", "crystal", "current", "curve", "customs", "daily", "damage", "dance", "danger", "dangerous", "dark", "darkness",
            "date", "daughter", "dawn", "dead", "deal", "dear", "death", "decide", "declared", "deep", "deeply", "deer", "definition",
            "degree", "depend", "depth", "describe", "desert", "design", "desk", "detail", "determine", "develop", "development", "diagram",
            "diameter", "difference", "different", "difficult", "difficulty", "digit", "dinner", "direct", "direction", "directly", "dirt", "dirty",
            "disappear", "discover", "discovery", "discuss", "discussion", "disease", "dish", "distance", "distant", "divide", "division", "doctor",
            "does", "done", "donkey", "door", "double", "doubt", "down", "dozen", "draw", "drawn", "dream", "dress", "drew",
            "dried", "drink", "drive", "driven", "driver", "driving", "drop", "dropped", "drove", "drug", "duck", "during",
            "duty", "each", "eager", "early", "earn", "earth", "easier", "easily", "east", "easy", "edge", "education",
            "effect", "effort", "eight", "either", "electric", "electricity", "element", "elephant", "eleven", "else", "empty", "energy",
            "engine", "engineer", "enjoy", "enough", "enter", "entire", "entirely", "environment", "equal", "equally", "equator", "equipment",
            "escape", "especially", "essential", "establish", "even", "evening", "event", "eventually", "ever", "every", "everybody", "everyone",
            "everything", "everywhere", "evidence", "exact", "exactly", "examine", "example", "excellent", "except", "exchange", "excited", "excitement",
            "exciting", "exclaimed", "exercise", "exist", "expect", "experience", "experiment", "explain", "explanation", "explore", "express", "expression",
            "extra", "face", "facing", "fact", "factor", "factory", "failed", "fair", "fairly", "fall", "fallen", "familiar",
            "family", "famous", "farm", "farmer", "farther", "fast", "fastened", "faster", "father", "favorite", "fear", "feathers",
            "feature", "feed", "feel", "feet", "fell", "fellow", "felt", "fence", "fewer", "field", "fierce", "fifteen",
            "fifth", "fifty", "fight", "fighting", "figure", "fill", "film", "final", "finally", "find", "fine", "finest",
            "finger", "finish", "fire", "fireplace", "firm", "first", "fish", "five", "fixed", "flag", "flame", "flat",
            "flew", "flies", "flight", "floating", "floor", "flow", "flower", "flown", "fluid", "flush", "food",
            "foot", "football", "force", "foreign", "forest", "forget", "forgot", "forgotten", "form", "former", "fort", "forth",
            "forty", "forward", "fought", "found", "four", "fourth", "frame", "free", "freedom", "frequently", "fresh", "friend",
            "friendly", "frighten", "frog", "from", "front", "frozen", "fruit", "fuel", "full", "fully", "function", "funny",
            "furniture", "further", "future", "gain", "game", "garage", "garden", "gate", "gather", "gave", "general", "generally",
            "gentle", "gently", "gets", "getting", "giant", "gift", "girl", "give", "given", "giving", "glad", "glass",
            "globe", "goes", "gold", "golden", "gone", "good", "goose", "government", "grabbed", "grade", "gradually", "grain",
            "grandfather", "grandmother", "graph", "grass", "gravity", "gray", "great", "greater", "greatest", "greatly", "green", "grew",
            "ground", "group", "grow", "grown", "growth", "guard", "guess", "guide", "gulf", "habit", "hair", "half",
            "halfway", "hall", "hand", "handle", "handsome", "hang", "happen", "happened", "happily", "happy", "harbor", "hard",
            "harder", "hardly", "harm", "hate", "have", "having", "head", "headed", "heading", "health", "heard", "hearing",
            "heart", "heat", "heavy", "height", "held", "hello", "help", "helpful", "herd", "here", "herself", "hidden",
            "hide", "high", "higher", "highest", "highway", "hill", "himself", "history", "hold", "hole", "hollow", "home",
            "honor", "hope", "horn", "horse", "hospital", "hour", "house", "however", "huge", "human", "hundred", "hung",
            "hungry", "hunt", "hunter", "hurried", "hurry", "hurt", "husband", "idea", "identity", "image", "imagine", "immediately",
            "importance", "important", "impossible", "improve", "inch", "include", "including", "income", "increase", "indeed", "independent", "indicate",
            "individual", "industrial", "industry", "influence", "information", "inside", "instance", "instant", "instead", "instrument", "interest", "interior",
            "into", "introduced", "invented", "involved", "iron", "island", "issue", "item", "itself", "jack", "jar",
            "join", "joined", "journey", "judge", "juice", "jump", "jungle", "just", "keep", "kept", "kettle", "kids",
            "kill", "kind", "kitchen", "knee", "knew", "knife", "knock", "know", "knowledge", "known", "label", "labor",
            "lack", "lady", "laid", "lake", "lamp", "land", "language", "large", "larger", "largest", "last", "late",
            "later", "laugh", "launch", "laws", "lead", "leader", "leaf", "learn", "learned", "least", "leather", "leave",
            "leaving", "left", "length", "less", "lesson", "letter", "level", "library", "life", "lift", "light", "like",
            "likely", "limited", "line", "lion", "lips", "liquid", "list", "listen", "little", "live", "living", "load",
            "local", "locate", "location", "lodge", "lone", "lonely", "long", "longer", "look", "loose", "lose", "loss",
            "lost", "loud", "love", "lovely", "lower", "luck", "lucky", "lunch", "lungs", "lying", "machine", "machinery",
            "made", "magic", "magnet", "mail", "main", "mainly", "major", "make", "making", "managed", "manner", "manufacturing",
            "many", "marc", "march", "mark", "market", "married", "mass", "massage", "master", "material", "mathematics", "matter",
            "maybe", "meal", "mean", "means", "meant", "measure", "meat", "medicine", "meet", "melted", "member", "memory",
            "mental", "merely", "mess", "message", "metal", "method", "mice", "middle", "might", "mighty", "mile", "military",
            "milk", "mill", "mind", "mine", "minerals", "minute", "mirror", "missing", "mission", "mistake", "mixture", "model",
            "modern", "molecular", "moment", "money", "monkey", "month", "mood", "moon", "more", "morning", "most", "mostly",
            "mother", "motion", "motor", "mountain", "mouse", "mouth", "move", "movement", "movie", "moving", "much", "multiply",
            "muscle", "music", "musical", "must", "myself", "mysterious", "nail", "name", "nation", "national", "native", "natural",
            "naturally", "nature", "near", "nearby", "nearer", "nearest", "nearly", "necessary", "neck", "needed", "needle", "needs",
            "negative", "neighbor", "neighborhood", "nervous", "nest", "never", "news", "next", "nice", "night", "nine",
            "nodded", "noise", "none", "noon", "nor", "north", "nose", "note", "noted", "nothing", "notice", "noun",
            "number", "numeral", "nuts", "object", "observe", "obtain", "occasionally", "occur", "ocean", "offer", "office", "officer",
            "official", "often", "once", "only", "onto", "open", "operation", "opinion", "opportunity", "opposite", "order", "ordinary",
            "organization", "organized", "origin", "original", "other", "ought", "ourselves", "outside", "over", "owner", "oxygen",
            "pack", "package", "page", "paid", "pain", "paint", "pair", "palace", "pale", "paper", "paragraph", "parallel",
            "parent", "park", "part", "particles", "particular", "particularly", "partly", "parts", "party", "pass", "passage", "past",
            "path", "pattern", "pause", "payment", "peace", "pencil", "people", "percent", "perfect", "perfectly", "perhaps", "period",
            "person", "personal", "phrase", "physical", "piano", "pick", "picture", "pictured", "piece", "pile", "pilot", "pine",
            "pink", "pipe", "pitch", "place", "plain", "plan", "plane", "planet", "planned", "planning", "plant", "plastic",
            "plate", "platform", "play", "pleasant", "please", "pleasure", "plenty", "plural", "plus", "pocket", "poem", "poet",
            "poetry", "point", "pole", "police", "policeman", "political", "pond", "pony", "pool", "poor", "popular", "population",
            "porch", "port", "position", "positive", "possible", "possibly", "post", "potato", "pound", "pour", "powder", "power",
            "powerful", "practical", "practice", "prepare", "present", "president", "press", "pressure", "pretty", "prevent", "previous", "price",
            "pride", "primitive", "principal", "principle", "printed", "private", "prize", "probably", "problem", "process", "produce", "product",
            "production", "program", "progress", "promised", "proper", "properly", "property", "protection", "proud", "prove", "provide", "public",
            "pull", "pupil", "pure", "purple", "purpose", "push", "putting", "puzzle", "quality", "quantity", "quarter", "queen",
            "question", "quick", "quickly", "quiet", "quietly", "quite", "rabbit", "race", "radio", "railroad", "rain", "raise",
            "ranch", "range", "rapidly", "rate", "rather", "reach", "read", "reader", "ready", "real", "realize", "reason",
            "recall", "receive", "recent", "recently", "recognize", "record", "recover", "refer", "refused", "region", "regular", "related",
            "relationship", "religious", "remain", "remarkable", "remember", "remove", "repeat", "replace", "replied", "report", "represent", "require",
            "research", "respect", "rest", "result", "return", "review", "rhyme", "rhythm", "rice", "rich", "ride", "riding",
            "right", "ring", "rise", "rising", "river", "road", "roar", "rock", "rocket", "rocky", "rode", "roll",
            "roof", "room", "root", "rope", "rose", "rough", "round", "route", "rule", "ruler", "running", "rush",
            "safe", "said", "sail", "sale", "salt", "same", "sand", "sang", "save", "saved", "scale", "scared",
            "scene", "school", "science", "scientific", "scientist", "score", "scout", "scratch", "screen", "screw", "search", "season",
            "seat", "second", "secret", "section", "seed", "seeing", "seek", "seem", "seen", "seldom", "select", "selection",
            "self", "sell", "send", "sense", "sent", "sentence", "separate", "series", "serious", "serve", "service", "sets",
            "setting", "settle", "settlers", "seven", "several", "shade", "shadow", "shake", "shaking", "shall", "shallow", "shape",
            "share", "sharp", "shaved", "sheep", "sheet", "shelf", "shells", "shelter", "shine", "shinning", "ship", "shirt",
            "shoe", "shoot", "shop", "shore", "short", "shorter", "shot", "should", "shoulder", "shout", "show", "shown",
            "shut", "sick", "sides", "sight", "sign", "signal", "silence", "silent", "silk", "silly", "silver", "similar",
            "simple", "simplest", "simply", "since", "sing", "single", "sink", "sister", "sitting", "situation", "size",
            "skill", "skin", "skull", "slave", "sleep", "slept", "slide", "slight", "slightly", "slip", "slipped", "slope",
            "slow", "slowly", "small", "smaller", "smallest", "smell", "smile", "smoke", "smooth", "snake", "snow", "soap",
            "social", "society", "soft", "softly", "soil", "solar", "sold", "soldier", "solid", "solution", "solve", "some",
            "somebody", "somehow", "someone", "something", "sometime", "somewhere", "song", "soon", "sort", "sound", "source", "south",
            "southern", "space", "speak", "special", "species", "specific", "speech", "speed", "spell", "spend", "spent", "spider",
            "spin", "spirit", "spite", "split", "spoken", "sport", "spot", "spread", "spring", "square", "stage", "stairs",
            "stand", "standard", "star", "stared", "start", "state", "statement", "station", "stay", "steady", "steam", "steel",
            "steep", "stems", "step", "stepped", "stick", "stiff", "still", "stock", "stomach", "stone", "stood", "stop",
            "stopped", "store", "storm", "story", "stove", "straight", "strange", "stranger", "straw", "stream", "street", "strength",
            "stretch", "strike", "string", "strip", "strong", "stronger", "struck", "structure", "struggle", "stuck", "student", "studied",
            "studying", "subject", "substance", "success", "successful", "such", "sudden", "suddenly", "sugar", "suggest", "suit", "summer",
            "supply", "support", "suppose", "sure", "surface", "surprise", "surrounded", "swam", "sweet", "swept", "swim", "swimming",
            "swing", "swung", "syllable", "symbol", "system", "table", "tail", "take", "taken", "tales", "talk", "tall",
            "tank", "tape", "task", "taste", "taught", "teach", "teacher", "team", "tears", "teeth", "telephone", "television",
            "tell", "temperature", "tent", "term", "terrible", "test", "than", "thank", "that", "thee", "them", "themselves",
            "then", "theory", "there", "therefore", "these", "they", "thick", "thin", "thing", "think", "third", "thirty",
            "this", "those", "thou", "though", "thought", "thousand", "thread", "three", "threw", "throat", "through", "throughout",
            "throw", "thrown", "thumb", "thus", "tide", "tight", "tightly", "till", "time", "tiny", "tire",
            "title", "tobacco", "today", "together", "told", "tomorrow", "tone", "tongue", "tonight", "tool", "tooth", "torn",
            "total", "touch", "toward", "tower", "town", "trace", "track", "trade", "traffic", "trail", "train", "transportation",
            "trap", "travel", "treated", "tree", "triangle", "tribe", "trick", "tried", "trip", "troops", "tropical", "trouble",
            "truck", "trunk", "truth", "tube", "tune", "turn", "twelve", "twenty", "twice", "type", "typical", "uncle",
            "under", "underline", "understanding", "unhappy", "union", "unit", "universe", "unknown", "unless", "until", "unusual", "upon",
            "upper", "upward", "useful", "using", "usual", "usually", "valley", "valuable", "value", "vapor", "variety",
            "various", "vast", "vegetable", "verb", "vertical", "very", "vessel", "victory", "view", "village", "visit", "visitor",
            "voice", "volume", "vote", "vowel", "voyage", "wagon", "wait", "walk", "wall", "want", "warm", "warn",
            "wash", "waste", "watch", "water", "wave", "weak", "wealth", "wear", "weather", "week", "weigh", "well",
            "went", "were", "west", "western", "whale", "what", "whatever", "wheat", "wheel", "when", "whenever", "where",
            "wherever", "whether", "which", "while", "whispered", "whistle", "white", "whole", "whom", "whose", "wide", "widely",
            "wife", "wild", "will", "willing", "wind", "window", "wing", "winter", "wire", "wise", "wish", "with",
            "within", "without", "wolf", "women", "wonder", "wonderful", "wood", "wooden", "wool", "word", "wore", "work",
            "worker", "world", "worried", "worry", "worse", "worth", "would", "wrapped", "write", "writer", "writing", "written",
            "wrong", "wrote", "yard", "year", "yellow", "yesterday", "yield", "young", "younger", "your", "yourself", "youth",
             "zero", "zone"
        ];

        // --- JavaScript functions (playSound, UI updates, generateUserID, generateElement, generateProblem, generateWord, createElementDiv, cleanupFallingElements, gameLoop, handleHitBottom, checkAnswer, gameOver, startGame, initiateGameLoop, updateModeButtons, showStartScreen, showLeaderboard, hideLeaderboard, unlockAudioContext) remain the same as the 'fixed' version ---
        // (Make sure all the functions from the previous 'fixed' response are included here)
        // --- Helper Function to Play Sound ---
        function playSound(soundElement) {
             if (!soundElement || !audioUnlocked) return;
             soundElement.currentTime = 0;
             soundElement.play().catch(error => console.warn(`Sound play blocked for ${soundElement.id}:`, error));
         }
        // --- UI Updates ---
        function updateScore() {
            scoreBoard.textContent = 'Score: ' + score;
            if (currentGameMode === 'math') {
                if (score >= 10 && operationTypes.length === 1) { operationTypes = ['add', 'subtract', 'multiply', 'divide']; }
                else if (score < 10 && operationTypes.length > 1) { operationTypes = ['add']; }
            }
        }
        function updateLivesDisplay() {
             livesBoard.textContent = 'Lives: ' + '❤️'.repeat(lives) + '🤍'.repeat(3 - lives); // Use Hearts
         }
        function updateFallSpeed() {
            const speedSteps = Math.floor(score / scoreIntervalForSpeedIncrease);
            let newSpeed = baseFallSpeed + (speedSteps * speedIncrement);
            fallSpeed = Math.min(newSpeed, maxFallSpeed);
        }
        // --- Generate Simulated Unique ID ---
        function generateUserID() {
            const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
            let id = '';
            for (let i = 0; i < 6; i++) { id += chars.charAt(Math.floor(Math.random() * chars.length)); }
            return id;
        }
        // --- Element Generation ---
        function generateElement() {
            if (isGameOver) return;
            if (currentElement && document.body.contains(currentElement)) return;
            cleanupFallingElements();
            if (currentGameMode === 'math') { generateProblem(); }
            else if (currentGameMode === 'typing') { generateWord(); }
        }
        function generateProblem() {
            const operation = operationTypes[Math.floor(Math.random() * operationTypes.length)];
            let num1, num2, problemText;
            switch (operation) {
                 case 'subtract': num1 = Math.floor(Math.random() * 15) + 1; num2 = Math.floor(Math.random() * num1) + 1; currentCorrectAnswer = num1 - num2; problemText = `${num1} - ${num2} =`; break;
                 case 'multiply': num1 = Math.floor(Math.random() * 10) + 1; num2 = Math.floor(Math.random() * 10) + 1; currentCorrectAnswer = num1 * num2; problemText = `${num1} × ${num2} =`; break;
                 case 'divide': const result = Math.floor(Math.random() * 10) + 1; num2 = Math.floor(Math.random() * 9) + 2; num1 = result * num2; currentCorrectAnswer = result; problemText = `${num1} ÷ ${num2} =`; break;
                 case 'add': default: num1 = Math.floor(Math.random() * 15) + 1; num2 = Math.floor(Math.random() * 15) + 1; currentCorrectAnswer = num1 + num2; problemText = `${num1} + ${num2} =`; break;
            }
            createElementDiv(problemText, 'math');
        }
        function generateWord() {
             if(wordList.length === 0) { console.error("Word list empty!"); return; }
             const randomIndex = Math.floor(Math.random() * wordList.length);
             let word = wordList[randomIndex];
             currentCorrectAnswer = word; // Store original word
             const displayWord = word.charAt(0).toUpperCase() + word.slice(1);
             createElementDiv(displayWord, 'word');
         }
        function createElementDiv(text, typeClass) {
             const elementDiv = document.createElement('div');
             elementDiv.classList.add('falling-element', typeClass);
             elementDiv.textContent = text;
             containerWidth = gameArea.offsetWidth;
             const elementWidthEstimate = text.length * (typeClass === 'word' ? 11 : 15);
             const maxLeft = containerWidth > 0 ? containerWidth - elementWidthEstimate - 20 : 300;
             const randomLeft = Math.max(10, Math.floor(Math.random() * Math.max(10, maxLeft)));
             elementDiv.style.left = randomLeft + 'px';
             elementDiv.style.top = '-60px';
             gameArea.appendChild(elementDiv);
             currentElement = elementDiv;
             requestAnimationFrame(() => { if (currentElement === elementDiv && !isGameOver) { elementDiv.classList.add('visible'); } });
         }
        function cleanupFallingElements() {
            const existingElements = gameArea.querySelectorAll('.falling-element');
            existingElements.forEach(el => el.remove());
            currentElement = null;
        }
        // --- Game Loop / Movement ---
        function gameLoop() {
             if (isGameOver) return;
             if (!currentElement) { initiateGameLoop(true); return; }
             if (containerHeight <= 0) { gameInterval = requestAnimationFrame(gameLoop); return; }
             let topPosition = currentElement.offsetTop;
             topPosition += fallSpeed;
             currentElement.style.top = topPosition + 'px';
             if (topPosition + currentElement.offsetHeight >= containerHeight) { handleHitBottom(); }
             else { if (!isGameOver) gameInterval = requestAnimationFrame(gameLoop); }
         }
        // --- Handle Hit Bottom ---
        function handleHitBottom() {
             if (isGameOver || !currentElement) return;
             const elementToRemove = currentElement; currentElement = null;
             if (elementToRemove) elementToRemove.remove();
             playSound(wrongSound); lives--; updateLivesDisplay(); // Update display with white heart
             gameContainer.style.backgroundColor = '#ffcdd2';
             setTimeout(() => { gameContainer.style.backgroundColor = '#ffffff'; }, 200);
             if (lives <= 0) { gameOver(); }
             else { if (!isGameOver) initiateGameLoop(true); }
        }
        // --- Answer Checking ---
        function checkAnswer() {
             if (isGameOver || !currentElement) { return; }
             const userAnswer = answerInput.value.trim();
             let isCorrect = false;
             if (currentGameMode === 'math') { const userNumber = parseInt(userAnswer); isCorrect = (!isNaN(userNumber) && userNumber === currentCorrectAnswer); }
             else if (currentGameMode === 'typing') { isCorrect = (userAnswer.toLowerCase() === currentCorrectAnswer?.toLowerCase()); }

             console.log(`Checking: Mode='${currentGameMode}', User='${userAnswer}', Correct='${currentCorrectAnswer}', Result=${isCorrect}`); // Keep debug log

             if (isCorrect) {
                playSound(correctSound); score++; updateScore(); updateFallSpeed(); answerInput.value = '';
                if (currentElement) { currentElement.style.opacity = '0'; currentElement.style.transform = 'scale(0.8)'; }
                const elementToRemove = currentElement; currentElement = null;
                setTimeout(() => { if(elementToRemove) elementToRemove.remove(); }, 200);
                if (!isGameOver) initiateGameLoop(true);
            } else {
                 playSound(wrongSound); lives--; updateLivesDisplay(); // Update display with white heart
                 answerInput.value = '';
                 answerInput.style.border = '2px solid red'; answerInput.classList.add('shake');
                 setTimeout(() => { answerInput.style.border = '2px solid #aed581'; answerInput.classList.remove('shake'); }, 500);
                 if (currentGameMode === 'math' && currentElement) { /* Flash red */ }
                 if (lives <= 0) { gameOver(); }
            }
             if (!isGameOver) { answerInput.focus(); }
         }
        // --- Game Over ---
        function gameOver() {
             if(isGameOver) return; isGameOver = true; console.log("Game Over!");
             if (gameInterval) cancelAnimationFrame(gameInterval); gameInterval = null;
             const elementToRemove = currentElement; currentElement = null;
             if(elementToRemove) elementToRemove.remove();
             gameArea.innerHTML = '';
             finalScoreDisplay.textContent = `Final Score: ${score}`; messageBoard.style.display = 'block';
             answerInput.disabled = true; submitButton.disabled = true;
             topBar.style.visibility = 'hidden'; modeSelector.style.visibility = 'hidden'; controlsDiv.style.visibility = 'hidden';
        }
        // --- Start Game Function ---
        function startGame(mode) {
            console.log(`Starting ${mode} game...`);
            containerHeight = gameArea.offsetHeight; containerWidth = gameArea.offsetWidth;
             if(containerHeight <= 0 || containerWidth <= 0) { console.warn("Start: Game area dimensions 0, retrying..."); setTimeout(() => startGame(mode), 50); return; }
            currentGameMode = mode; updateModeButtons();
            isGameOver = false; score = 0; lives = 3; fallSpeed = baseFallSpeed;
            if (currentGameMode === 'math') { operationTypes = ['add']; }
            updateScore(); updateLivesDisplay(); updateFallSpeed(); // Initial UI setup
            answerInput.value = ''; answerInput.disabled = false; submitButton.disabled = false;
            answerInput.placeholder = (currentGameMode === 'math') ? 'Answer' : 'Type word...';
            answerInput.type = 'text';
            if (currentGameMode === 'math') { answerInput.setAttribute('inputmode', 'numeric'); answerInput.setAttribute('pattern', '[0-9]*'); }
            else { answerInput.removeAttribute('inputmode'); answerInput.removeAttribute('pattern'); }
            messageBoard.style.display = 'none'; startScreen.classList.add('hidden'); leaderboardScreen.classList.add('hidden');
            topBar.style.visibility = 'visible'; modeSelector.style.visibility = 'visible'; controlsDiv.style.visibility = 'visible';
            if (gameInterval) { cancelAnimationFrame(gameInterval); gameInterval = null; }
            gameArea.innerHTML = ''; currentElement = null;
            currentUserID = generateUserID(); userIdDisplay.textContent = `ID: ${currentUserID}`;
            initiateGameLoop();
            answerInput.focus();
        }
        // --- Helper to start/restart the game loop ---
        function initiateGameLoop(isRestart = false) {
             if (isGameOver) { console.log("Loop initiation stopped: Game Over."); return; }
             if (gameInterval && !isRestart) cancelAnimationFrame(gameInterval);
             generateElement();
             if (currentElement && !isGameOver) { gameInterval = requestAnimationFrame(gameLoop); }
             else if (!isGameOver) { console.warn("Failed init, retrying loop."); setTimeout(() => initiateGameLoop(isRestart), 100); }
        }
        // --- Function to update active mode button style ---
        function updateModeButtons() {
            if (currentGameMode === 'math') { modeMathButton.classList.add('active'); modeTypingButton.classList.remove('active'); }
            else { modeTypingButton.classList.add('active'); modeMathButton.classList.remove('active'); }
             if (!isGameOver) {
                 if (gameInterval) cancelAnimationFrame(gameInterval); gameInterval = null;
                 cleanupFallingElements(); currentElement = null;
                 score = 0; updateScore(); lives = 3; updateLivesDisplay();
                 fallSpeed = baseFallSpeed; updateFallSpeed();
                 if (currentGameMode === 'math') { operationTypes = ['add']; }
                 answerInput.value = '';
                 answerInput.placeholder = (currentGameMode === 'math') ? 'Answer' : 'Type word...';
                 answerInput.type = 'text';
                 if (currentGameMode === 'math') { answerInput.setAttribute('inputmode', 'numeric'); answerInput.setAttribute('pattern', '[0-9]*'); }
                 else { answerInput.removeAttribute('inputmode'); answerInput.removeAttribute('pattern'); }
                 initiateGameLoop();
                 answerInput.focus();
             }
        }
        // --- Function to return to start screen ---
         function showStartScreen() {
             if (gameInterval) cancelAnimationFrame(gameInterval); gameInterval = null;
             isGameOver = true; currentElement = null;
             messageBoard.style.display = 'none'; leaderboardScreen.classList.add('hidden'); gameArea.innerHTML = '';
             topBar.style.visibility = 'hidden'; modeSelector.style.visibility = 'hidden'; controlsDiv.style.visibility = 'hidden';
             startScreen.style.display = 'flex'; startScreen.classList.remove('hidden');
             console.log("Exited to Start Screen");
         }
        // --- Leaderboard Toggle Functions ---
        function showLeaderboard() { leaderboardScreen.style.display = 'flex'; leaderboardScreen.classList.remove('hidden'); startScreen.classList.add('hidden'); }
        function hideLeaderboard() { leaderboardScreen.classList.add('hidden'); startScreen.style.display = 'flex'; startScreen.classList.remove('hidden'); }
        // --- Initial Setup & Audio Unlock ---
        function unlockAudioContext() {
             if (!audioUnlocked) {
                 console.log("Attempting audio unlock...");
                 let p1 = correctSound.play(); if (p1) p1.then(_ => { correctSound.pause(); correctSound.currentTime = 0; }).catch(()=>{});
                 let p2 = wrongSound.play(); if (p2) p2.then(_ => { wrongSound.pause(); wrongSound.currentTime = 0; audioUnlocked = true; }).catch(()=>{});
                 if(!p1 || !p2) audioUnlocked = true;
             }
         }
        window.onload = () => {
           console.log("Page loaded.");
           initialStartButton.addEventListener('click', () => {
                 unlockAudioContext(); startScreen.classList.add('hidden');
                 setTimeout(() => {
                     topBar.style.visibility = 'visible'; modeSelector.style.visibility = 'visible'; controlsDiv.style.visibility = 'visible';
                     startGame(currentGameMode || 'math');
                 }, 50);
            });
            modeMathButton.addEventListener('click', () => { if (currentGameMode !== 'math' && !isGameOver) { startGame('math'); } });
            modeTypingButton.addEventListener('click', () => { if (currentGameMode !== 'typing' && !isGameOver) { startGame('typing'); } });
            showLeaderboardButton.addEventListener('click', showLeaderboard);
            closeLeaderboardButton.addEventListener('click', hideLeaderboard);
            exitButton.addEventListener('click', showStartScreen);
            // Add listeners for submit button and enter key
            submitButton.addEventListener('click', checkAnswer);
            answerInput.addEventListener('keypress', function(event) { if (event.key === 'Enter') { checkAnswer(); } });
        };
    </script>

</body>
</html
