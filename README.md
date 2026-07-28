Касса Тест!
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Тестирование: Закрытие смены и кассовые операции</title>
    <style>
        body { font-family: Arial, sans-serif; background-color: #f4f6f9; margin: 0; padding: 20px; color: #333; }
        .container { max-width: 650px; margin: 30px auto; background: #fff; padding: 30px; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); }
        h1 { font-size: 20px; color: #1a568c; text-align: center; margin-top: 0; margin-bottom: 15px; }
        
        /* Таймер и Статистика */
        .header-info { display: flex; justify-content: space-between; align-items: center; margin-bottom: 10px; font-size: 15px; font-weight: bold; }
        .timer { color: #d9534f; background: #fdf2f2; padding: 5px 12px; border-radius: 6px; border: 1px solid #f5c6cb; }
        .stats { color: #666; }

        /* Прогресс-бар */
        .progress-container { background-color: #e0e0e0; border-radius: 10px; height: 10px; width: 100%; margin-bottom: 20px; overflow: hidden; }
        .progress-bar { background-color: #28a745; height: 100%; width: 0%; transition: width 0.3s ease; }

        /* Карточка вопроса */
        .question-card { display: block; }
        .question-text { font-weight: bold; margin-bottom: 20px; font-size: 17px; line-height: 1.4; color: #2c3e50; }
        
        /* Варианты ответов */
        .options { list-style: none; padding: 0; margin: 0; }
        .options li { margin-bottom: 12px; }
        .btn-option { 
            width: 100%; 
            padding: 14px 16px; 
            text-align: left; 
            font-size: 15px; 
            border: 2px solid #e2e8f0; 
            border-radius: 8px; 
            background: #fff; 
            cursor: pointer; 
            transition: all 0.2s ease;
            box-sizing: border-box;
            color: #333;
        }
        .btn-option:hover:not([disabled]) { border-color: #3182ce; background-color: #ebf8ff; }
        
        /* Проверка ответов */
        .btn-option.correct { background-color: #c6f6d5 !important; border-color: #38a169 !important; color: #22543d; font-weight: bold; }
        .btn-option.incorrect { background-color: #fed7d7 !important; border-color: #e53e3e !important; color: #742a2a; }
        
        /* Кнопка */
        .action-btn { 
            display: none; 
            width: 100%; 
            padding: 14px; 
            background-color: #3182ce; 
            color: white; 
            border: none; 
            border-radius: 8px; 
            font-size: 16px; 
            font-weight: bold; 
            cursor: pointer; 
            margin-top: 20px; 
            transition: background 0.2s;
        }
        .action-btn:hover { background-color: #2b6cb0; }

        /* Экран результатов */
        .result-box { display: none; text-align: center; padding: 10px; }
        .result-box h2 { font-size: 26px; margin-bottom: 10px; }
        .result-box p { font-size: 18px; margin-bottom: 20px; }
        .praise-text { font-size: 22px; font-weight: bold; margin-bottom: 20px; }
        .praise-success { color: #28a745; }
        .praise-fail { color: #d9534f; }
        .success-title { color: #38a169; }
        .fail-title { color: #e53e3e; }
        .restart-btn { background-color: #28a745; }
        .restart-btn:hover { background-color: #218838; }
    </style>
</head>
<body>

<div class="container">
    <h1>Тест: Закрытие смены и кассовые операции</h1>
    
    <div id="quiz-screen">
        <div class="header-info">
            <div class="timer" id="timer-display">Осталось времени: 30:00</div>
            <div class="stats" id="question-number">Вопрос 1 из 25</div>
        </div>

        <div class="progress-container">
            <div class="progress-bar" id="progress-bar"></div>
        </div>

        <div class="question-card">
            <div class="question-text" id="question-text">Загрузка...</div>
            <ul class="options" id="options-list"></ul>
        </div>

        <button class="action-btn" id="next-btn" onclick="nextQuestion()">Следующий вопрос</button>
    </div>

    <div id="result-screen" class="result-box">
        <h2 id="result-title"></h2>
        <div id="praise-box" class="praise-text"></div>
        <p id="result-score"></p>
        <button class="action-btn restart-btn" style="display:block;" onclick="initQuiz()">Пройти заново</button>
    </div>
</div>

<script>
const baseTemplates = [
    { q: "Какое значение должна всегда иметь 'Контрольная цифра' при детальной сверке?", options: ["0", "1", "Сумме Z-отчета", "100"], correct: 0 },
    { q: "Что ЗАПРЕЩЕНО делать кассиру при статусе «Отменен» у документа Розничная реализация в 1С?", options: ["Уходить домой", "Печатать Z-отчет", "Пересчитывать кассу", "Проводить РТиУ"], correct: 0 },
    { q: "Какое действие обязательно при закрытии смены на кассовом аппарате при предложении изъять денежные средства?", options: ["Подтверждать всегда, касса изымается в 0", "Отменять изъятие", "Изымать только половину", "Оставлять остаток на завтра"], correct: 0 },
    { q: "В каком случае при возврате маркированного товара СИ касса запрашивает сканирование марки?", options: ["Если у штрихкода есть признак маркировки", "Всегда без исключений", "Только при возврате по безналу", "Никогда не запрашивает"], correct: 0 },
    { q: "Какой короткий код используется для вызова произвольного возврата за услугу на кассе?", options: ["55555", "11111", "00000", "77777"], correct: 0 },
    { q: "Какое условие является ключевым для проведения возврата 'день в день'?", options: ["Сегодня купил — сегодня вернул, на той же кассе и тем же способом оплаты", "Возврат на любой кассе в течение 14 дней", "Наличие паспорта и письменного заявления", "Обязательное проведение через РКО"], correct: 0 },
    { q: "Каков максимальный процент оплаты товара/услуги обычным бонусом по Бонусной карте работника?", options: ["Не более 70%", "Не более 99%", "100%", "Не более 50%"], correct: 0 },
    { q: "В течение скольких дней должны быть использованы бонусы с Премиум-счета карты работника?", options: ["30 календарных дней", "14 дней", "90 дней", "1 год"], correct: 0 },
    { q: "Чему равен 1 бонус на Бонусной карте работника?", options: ["1 белорусскому рублю", "10 белорусским рублям", "0.5 белорусского рубля", "5 белорусским рублям"], correct: 0 },
    { q: "Какая стоимость услуги 'Доставка на дом' установлена регламентом независимо от суммы покупки?", options: ["19.00 бел. рублей", "15.00 бел. рублей", "Бесплатно от 100 рублей", "25.00 бел. рублей"], correct: 0 },
    { q: "В радиусе скольких километров от города осуществляется доставка на дом?", options: ["До 60 км", "До 50 км", "До 100 км", "До 30 км"], correct: 0 },
    { q: "Где проверяется наличие выгрузки последнего чека перед закрытием смены?", options: ["В программе КИТ (КИТ-Чеки ККМ)", "В Excel-файле", "В личной почте", "В терминале банка"], correct: 0 },
    { q: "Какой отчет снимается в кассе в момент работы без закрытия смены для проверки остатка?", options: ["Х-отчет", "Z-отчет", "Товарный отчет", "Терминальный отчет"], correct: 0 },
    { q: "В какой документ в 1С откроется доступ для установки галочки «Закрытие смены»?", options: ["Розничная реализация", "Заказ КИМ", "РТиУ", "ПКО"], correct: 0 },
    { q: "Что входит в формулу расчета 'Оплата безнал' при сверке Z-отчета и 1С?", options: ["Продажа Банк. пл. картой (сумма со всех касс) - возврат день в день по банковской карте", "Только Z-отчет кассы №1", "Сумма наличных + кредиты", "Итог сменных продаж"], correct: 0 },
    { q: "Какая ошибка возникает, если итоговая реализация в 1С меньше, чем в Z-отчете на сумму одного чека?", options: ["Недогрузка последней продажи", "Задвоение чека", "Ошибка серийного номера", "Не удалились позиции после возврата"], correct: 0 },
    { q: "Что нужно сделать при обнаружении ошибки 'Задвоение чека' в Розничной реализации?", options: ["Удалить дублирующую строку", "Провести документ повторно", "Добавить еще один чек", "Выполнить изъятие"], correct: 0 },
    { q: "По какому пути в 1С формируется 'Отчет по закрытию смены'?", options: ["Отчеты -> Отчеты: Закрытие смены -> Отчет по закрытию смены (отчет по магазину)", "Продажи -> Отчеты -> Касса", "Сервис -> Закрытие смены", "Склад -> Документы закрытия"], correct: 0 },
    { q: "На основании какого документа формируется 'Отчет по закрытию смены'?", options: ["Бухгалтерский отчет комиссионера", "Z-отчет", "Приходный кассовый ордер", "Акт сверки"], correct: 0 },
    { q: "Что нужно сделать, если свободный остаток в отчете показывает '-1'?", options: ["Проверить фактическую продажу, забрать партию и заменить номенклатуру", "Списать товар как брак", "Проигнорировать и закрыть смену", "Удалить Розничную реализацию"], correct: 0 },
    { q: "Как найти Заявку на сервис, если при закрытии смены выходит ошибка 'Не заполнена Заявка на сервис'?", options: ["Через Заказ КИМ на вкладке 'Заявки на сервис'", "Создать новую РТиУ", "В терминале Оплати", "В Z-отчете"], correct: 0 },
    { q: "При каком способе оплаты заказа КИМ покупатель должен предъявить QR-код?", options: ["OnlinerPay", "ЕРИП", "Кредит в банке", "Оплата наличными"], correct: 0 },
    { q: "Чем подтверждается оплата заказа через систему ЕРИП при выдаче?", options: ["Кодом платежа (равен номеру заказа КИМ в 1С8)", "Паспортом и доверенностью", "Терминальным чеком", "Гарантийным талоном"], correct: 0 },
    { q: "На сколько дней максимум можно продлить заказ КИМ по просьбе клиента?", options: ["Не более чем на 7 дней", "До 30 дней", "На 3 дня", "Продление запрещено"], correct: 0 },
    { q: "В каких экземплярах распечатывается 'Акт приемки товара' при выдаче оплаченного заказа КИМ?", options: ["В 2-х экземплярах", "В 1 экземпляре", "В 3-х экземплярах", "Не распечатывается"], correct: 0 }
];

let fullQuestionBank = [];

function generate600Questions() {
    fullQuestionBank = [];
    let count = 1;
    for (let round = 1; round <= 25; round++) {
        for (let item of baseTemplates) {
            let qText = item.q;
            if (round > 1) {
                qText = `[Ситуация №${count}] ${item.q}`;
            }
            fullQuestionBank.push({
                id: count,
                q: qText,
                options: [...item.options],
                correct: item.correct
            });
            count++;
        }
    }
}

let currentQuestions = [];
let currentIndex = 0;
let score = 0;
let timeLeft = 1800; // 30 минут в секундах
let timerInterval = null;

function shuffle(array) {
    let arr = [...array];
    for (let i = arr.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [arr[i], arr[j]] = [arr[j], arr[i]];
    }
    return arr;
}

function startTimer() {
    clearInterval(timerInterval);
    timeLeft = 1800; // 30 минут
    updateTimerDisplay();

    timerInterval = setInterval(() => {
        timeLeft--;
        updateTimerDisplay();

        if (timeLeft <= 0) {
            clearInterval(timerInterval);
            showResults(true); // Автоматическое завершение по таймауту
        }
    }, 1000);
}

function updateTimerDisplay() {
    const minutes = Math.floor(timeLeft / 60);
    const seconds = timeLeft % 60;
    const formattedSeconds = seconds < 10 ? `0${seconds}` : seconds;
    const formattedMinutes = minutes < 10 ? `0${minutes}` : minutes;
    document.getElementById('timer-display').innerText = `Осталось времени: ${formattedMinutes}:${formattedSeconds}`;
}

function initQuiz() {
    if (fullQuestionBank.length === 0) {
        generate600Questions();
    }
    
    let shuffledBank = shuffle(fullQuestionBank);
    currentQuestions = shuffledBank.slice(0, 25);
    
    currentIndex = 0;
    score = 0;

    document.getElementById('quiz-screen').style.display = 'block';
    document.getElementById('result-screen').style.display = 'none';

    startTimer();
    showQuestion();
}

function showQuestion() {
    const q = currentQuestions[currentIndex];
    
    document.getElementById('question-number').innerText = `Вопрос ${currentIndex + 1} из ${currentQuestions.length}`;
    document.getElementById('progress-bar').style.width = `${((currentIndex) / currentQuestions.length) * 100}%`;
    
    document.getElementById('question-text').innerText = q.q;
    
    const optionsList = document.getElementById('options-list');
    optionsList.innerHTML = '';

    let optionsWithIndex = q.options.map((opt, i) => ({ text: opt, isCorrect: i === q.correct }));
    optionsWithIndex = shuffle(optionsWithIndex);

    optionsWithIndex.forEach(optObj => {
        const li = document.createElement('li');
        const btn = document.createElement('button');
        btn.className = 'btn-option';
        btn.innerText = optObj.text;
        btn.onclick = () => selectAnswer(btn, optObj.isCorrect);
        li.appendChild(btn);
        optionsList.appendChild(li);
    });

    document.getElementById('next-btn').style.display = 'none';
}

function selectAnswer(selectedBtn, isCorrect) {
    const buttons = document.querySelectorAll('.btn-option');
    buttons.forEach(btn => btn.disabled = true);

    if (isCorrect) {
        selectedBtn.classList.add('correct');
        score++;
    } else {
        selectedBtn.classList.add('incorrect');
        const q = currentQuestions[currentIndex];
        buttons.forEach(btn => {
            if (btn.innerText === q.options[q.correct]) {
                btn.classList.add('correct');
            }
        });
    }

    const nextBtn = document.getElementById('next-btn');
    if (currentIndex === currentQuestions.length - 1) {
        nextBtn.innerText = 'Посмотреть результаты';
    } else {
        nextBtn.innerText = 'Следующий вопрос';
    }
    nextBtn.style.display = 'block';
}

function nextQuestion() {
    currentIndex++;
    if (currentIndex < currentQuestions.length) {
        showQuestion();
    } else {
        showResults(false);
    }
}

function showResults(isTimeOut = false) {
    clearInterval(timerInterval);
    
    document.getElementById('quiz-screen').style.display = 'none';
    const resultScreen = document.getElementById('result-screen');
    resultScreen.style.display = 'block';

    const percent = Math.round((score / currentQuestions.length) * 100);
    const title = document.getElementById('result-title');
    const praiseBox = document.getElementById('praise-box');
    const scoreText = document.getElementById('result-score');

    if (percent >= 80 && !isTimeOut) {
        title.innerText = 'Тест успешно сдан!';
        title.className = 'success-title';
        praiseBox.innerText = 'Ты молодец! 🎉';
        praiseBox.className = 'praise-text praise-success';
    } else {
        title.innerText = isTimeOut ? 'Время вышло! Тест не сдан' : 'Тест не сдан';
        title.className = 'fail-title';
        praiseBox.innerText = 'Не переживай, попробуй ещё!';
        praiseBox.className = 'praise-text praise-fail';
    }

    scoreText.innerText = `Ваш результат: ${score} из ${currentQuestions.length} (${percent}%). Проходной балл — 80%.`;
}

window.onload = initQuiz;
</script>
</body>
</html>
