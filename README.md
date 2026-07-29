
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Тестирование Кассиров — Сеть «5 Элемент»</title>
    <style>
        :root {
            --primary: #e30613;
            --primary-dark: #b8030e;
            --bg: #f4f6f8;
            --card-bg: #ffffff;
            --text: #2c3e50;
            --border: #dcdfe6;
            --success: #27ae60;
            --success-bg: #e8f8f0;
            --danger: #e74c3c;
            --danger-bg: #fde8e8;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--bg);
            color: var(--text);
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
        }

        .container {
            width: 100%;
            max-width: 850px;
            background: var(--card-bg);
            border-radius: 12px;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
            padding: 30px;
            box-sizing: border-box;
        }

        header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-bottom: 2px solid var(--bg);
            padding-bottom: 15px;
            margin-bottom: 25px;
        }

        h1 {
            font-size: 22px;
            margin: 0;
            color: var(--primary);
        }

        .timer-box {
            font-size: 18px;
            font-weight: bold;
            background: #fff3f3;
            color: var(--primary);
            padding: 8px 16px;
            border-radius: 20px;
            border: 1px solid var(--primary);
        }

        .start-screen, .quiz-screen, .result-screen {
            display: none;
        }

        .active {
            display: block;
        }

        .btn {
            background-color: var(--primary);
            color: white;
            border: none;
            padding: 12px 24px;
            font-size: 16px;
            font-weight: 600;
            border-radius: 6px;
            cursor: pointer;
            transition: background 0.2s;
            margin-top: 15px;
        }

        .btn:hover:not(:disabled) {
            background-color: var(--primary-dark);
        }

        .btn:disabled {
            background-color: #bdc3c7;
            cursor: not-allowed;
        }

        .btn-secondary {
            background-color: #7f8c8d;
            margin-right: 10px;
        }

        .btn-secondary:hover:not(:disabled) {
            background-color: #616e6f;
        }

        .question-card {
            margin-bottom: 25px;
            padding: 20px;
            background: #fafafa;
            border-radius: 8px;
            border-left: 4px solid var(--primary);
        }

        .question-header {
            display: flex;
            justify-content: space-between;
            font-size: 14px;
            color: #7f8c8d;
            margin-bottom: 10px;
            font-weight: 600;
        }

        .question-title {
            font-size: 18px;
            font-weight: 600;
            margin-bottom: 16px;
            line-height: 1.4;
        }

        .options-list {
            list-style: none;
            padding: 0;
            margin: 0;
        }

        .option-item {
            margin-bottom: 10px;
        }

        .option-label {
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 12px 16px;
            background: white;
            border: 1px solid var(--border);
            border-radius: 6px;
            cursor: pointer;
            transition: border-color 0.2s, background 0.2s;
        }

        .option-content {
            display: flex;
            align-items: center;
        }

        .option-label:hover:not(.disabled) {
            border-color: var(--primary);
            background: #fff9f9;
        }

        .option-label input {
            margin-right: 12px;
            width: 18px;
            height: 18px;
        }

        .option-label.correct {
            background-color: var(--success-bg) !important;
            border-color: var(--success) !important;
            color: #1e7e34;
            font-weight: 600;
        }

        .option-label.incorrect {
            background-color: var(--danger-bg) !important;
            border-color: var(--danger) !important;
            color: #bd2130;
        }

        .option-label.disabled {
            cursor: not-allowed;
            opacity: 0.9;
        }

        .status-icon {
            font-weight: bold;
            font-size: 18px;
        }

        .correct .status-icon {
            color: var(--success);
        }

        .incorrect .status-icon {
            color: var(--danger);
        }

        .progress-bar {
            height: 8px;
            background: var(--border);
            border-radius: 4px;
            margin-bottom: 25px;
            overflow: hidden;
        }

        .progress-fill {
            height: 100%;
            background: var(--primary);
            width: 0%;
            transition: width 0.3s;
        }

        .nav-buttons {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .stats-grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 15px;
            margin: 20px 0;
        }

        .stat-box {
            padding: 15px;
            border-radius: 8px;
            text-align: center;
            background: #fafafa;
            border: 1px solid var(--border);
        }

        .stat-value {
            font-size: 26px;
            font-weight: bold;
            margin-top: 5px;
        }

        .stat-correct .stat-value { color: var(--success); }
        .stat-incorrect .stat-value { color: var(--danger); }
        .stat-total .stat-value { color: var(--primary); }

        .result-status {
            font-size: 22px;
            font-weight: bold;
            text-align: center;
            margin: 15px 0;
        }

        .review-item {
            padding: 12px 16px;
            margin-bottom: 12px;
            border-radius: 6px;
        }

        .review-correct {
            background-color: var(--success-bg);
            border: 1px solid var(--success);
        }

        .review-incorrect {
            background-color: var(--danger-bg);
            border: 1px solid var(--danger);
        }
    </style>
</head>
<body>

<div class="container">
    <header>
        <h1>Проверка знаний кассира</h1>
        <div class="timer-box" id="timer">25:00</div>
    </header>

    <!-- Экран старта -->
    <div id="start-screen" class="start-screen active">
        <h2>Инструкция к тестированию</h2>
        <ul>
            <li>Вам предстоит ответить на <b>25 случайных вопросов</b> из общей базы.</li>
            <li>При выборе ответа система <b>сразу подсвечивает результат</b>.</li>
            <li>Вопросы идут <b>последовательно один за другим</b>.</li>
            <li>На прохождение теста отведен таймер: <b>25 минут</b>.</li>
            <li>Все вопросы и варианты ответов <b>случайно перемешиваются</b> при каждом новом тесте.</li>
            <li>Для сдачи теста необходимо набрать не менее <b>80% правильных ответов (20 из 25)</b>.</li>
        </ul>
        <button class="btn" onclick="startTest()">Начать тест</button>
    </div>

    <!-- Экран теста -->
    <div id="quiz-screen" class="quiz-screen">
        <div class="progress-bar">
            <div class="progress-fill" id="progress"></div>
        </div>
        
        <div id="single-question-container"></div>

        <div class="nav-buttons">
            <button class="btn btn-secondary" id="btn-prev" onclick="prevQuestion()" disabled>Назад</button>
            <button class="btn" id="btn-next" onclick="nextQuestion()" disabled>Далее</button>
        </div>
    </div>

    <!-- Экран результатов -->
    <div id="result-screen" class="result-screen">
        <h2>Результаты тестирования</h2>
        
        <div class="result-status" id="result-status-text"></div>

        <div class="stats-grid">
            <div class="stat-box stat-total">
                <div>Всего вопросов</div>
                <div class="stat-value" id="stat-total">0</div>
            </div>
            <div class="stat-box stat-correct">
                <div>Правильных</div>
                <div class="stat-value" id="stat-correct">0</div>
            </div>
            <div class="stat-box stat-incorrect">
                <div>Неправильных</div>
                <div class="stat-value" id="stat-incorrect">0</div>
            </div>
        </div>

        <h3>Детализация ответов:</h3>
        <div id="review-container"></div>
        
        <button class="btn" onclick="restartTest()">Пройти повторно (новые вопросы)</button>
    </div>
</div>

<script>
// ГЕНЕРАТОР БАЗЫ ВОПРОСОВ (Расширенный список уникальных вопросов)
const baseTemplates = [
    {
        q: "Чему всегда должна быть равна «Контрольная цифра» в отчете по закрытию смены?",
        options: ["0", "1", "Сумме наличных денег", "Сумме безналичных продаж"],
        answer: "0"
    },
    {
        q: "При каком статусе документа «Розничная реализация» кассиру СТРОГО запрещено уходить домой?",
        options: ["Отменен", "Проведен", "Не подтвержден", "Черновик"],
        answer: "Отменен"
    },
    {
        q: "Каким действием кассир должен завершить процедуру закрытия смены на кассовом аппарате?",
        options: ["Подтвердить всегда изъятие в 0", "Оставить деньги на завтра", "Изъять ровно половину суммы", "Снять только Х-отчет"],
        answer: "Подтвердить всегда изъятие в 0"
    },
    {
        q: "Где в программе КИТ проверить наличие последнего оплаченного чека перед закрытием смены?",
        options: ["КИТ - Чеки ККМ", "Сервис -> Отчеты", "Журнал БСО", "Настройки принтера"],
        answer: "КИТ - Чеки ККМ"
    },
    {
        q: "Какая операция на кассе применяется, если клиент возвращает товар «сегодня купил — сегодня вернул»?",
        options: ["Возврат по чеку (день в день)", "Произвольный возврат", "Изъятие", "Акт утилизации"],
        answer: "Возврат по чеку (день в день)"
    },
    {
        q: "В каком случае при оформлении возврата за товар/услугу применяется «Произвольный возврат»?",
        options: ["Товар куплен ранее текущего дня", "Товар куплен сегодня", "При оплате картой сотрудника", "Товар бракованный"],
        answer: "Товар куплен ранее текущего дня"
    },
    {
        q: "Какова фиксированная стоимость услуги «Доставка на дом» независимо от стоимости покупки?",
        options: ["19.00 бел. рублей", "Бесплатно", "10.00 бел. рублей", "25.00 бел. рублей"],
        answer: "19.00 бел. рублей"
    },
    {
        q: "Какой лимит оплаты бонусами установлен для «Премиум счета» Бонусной карты работника?",
        options: ["Не более 99%", "Не более 50%", "Не более 70%", "100%"],
        answer: "Не более 99%"
    },
    {
        q: "Какой лимит оплаты бонусами установлен для «Обычного счета» Бонусной карты работника?",
        options: ["Не более 70%", "Не более 30%", "Не более 50%", "Не более 99%"],
        answer: "Не более 70%"
    },
    {
        q: "Каков срок действия бонусов, начисляемых на счет «Премиум» Бонусной карты работника?",
        options: ["30 календарных дней", "10 дней", "14 дней", "1 год"],
        answer: "30 календарных дней"
    },
    {
        q: "Разрешено ли использовать Бонусную карту работника при покупке товара в кредит/рассрочку?",
        options: ["Запрещено", "Да, без ограничений", "Только с разрешения заведующего", "Только для товаров до 100 рублей"],
        answer: "Запрещено"
    },
    {
        q: "Что необходимо сделать, если при снятии Х-отчета обнаружен свободный остаток «-1» в розничной реализации?",
        options: ["Заменить номенклатуру в рознице и подставить партию товара", "Заблокировать кассу", "Удалить чек", "Оформить утилизацию"],
        answer: "Заменить номенклатуру в рознице и подставить партию товара"
    },
    {
        q: "Какое требование к кассе обязательно при возврате чека «день в день»?",
        options: ["Строго на той кассе, где производился расчет", "На любой свободной кассе магазина", "На кассе администратора", "Только в 1С"],
        answer: "Строго на той кассе, где производился расчет"
    },
    {
        q: "При оформлении возврата маркированного товара СИ, что запрашивает касса в первую очередь при отсутствии марки в ПЧ?",
        options: ["Сканирование штрихкода товара (EAN13)", "Паспорт покупателя", "Номер договора", "Код PATIO5"],
        answer: "Сканирование штрихкода товара (EAN13)"
    },
    {
        q: "Что обязательно должна содержать доверенность от организации при получении товара по безналичному расчету?",
        options: ["Подпись директора, гл. бухгалтера, наименование, количество, дату и срок действия", "Только печать компании", "Только подпись водителя", "Чек ККМ"],
        answer: "Подпись директора, гл. бухгалтера, наименование, количество, дату и срок действия"
    },
    {
        q: "Какое условие по весу товара установлено для сотрудников службы доставки при переносе на расстояние более 50 метров?",
        options: ["Не осуществляют перенос весом свыше 15 кг на расстояние > 50 м", "До 5 кг", "До 10 кг", "Ограничений нет"],
        answer: "Не осуществляют перенос весом свыше 15 кг на расстояние > 50 м"
    },
    {
        q: "На какой радиус от границы города распространяется зона стандартной доставки регионов?",
        options: ["До 60 км", "До 10 км", "До 30 км", "До 100 км"],
        answer: "До 60 км"
    },
    {
        q: "В каком статусе должна находиться задача в рабочем месте СПВ после корректного сканирования маркировки в МП?",
        options: ["Выдан", "К выдаче", "К доставке", "Отменен"],
        answer: "Выдан"
    },
    {
        q: "Где формируется документ «Отчет по закрытию смены» в 1С8?",
        options: ["Отчеты -> Отчеты: Закрытие смены -> Отчет по закрытию смены", "Продажи -> Отчеты", "Сервис -> Касса", "Склад -> Документы"],
        answer: "Отчеты -> Отчеты: Закрытие смены -> Отчет по закрытию смены"
    },
    {
        q: "Что нужно сделать при обнаружении ошибки «Задвоение чека» в 1С?",
        options: ["Удалить дублирующую строку", "Провести повторный чек", "Изменить вид оплаты на сертификат", "Сделать произвольный возврат"],
        answer: "Удалить дублирующую строку"
    },
    {
        q: "При продаже со «Склада образцов» какой признак автоматически проставляется при выписке ПЧ?",
        options: ["Доставка", "Самовывоз", "Утилизация", "Срочно"],
        answer: "Доставка"
    },
    {
        q: "Какой короткий код используется для вызова службы коррекции цены при произвольном возврате за услугу?",
        options: ["55555", "11111", "77777", "99999"],
        answer: "55555"
    },
    {
        q: "Через какую программу вносится код подтверждения из СМС при выписке заказа по безналу?",
        options: ["Терминал МКП (mkp.patio-minsk.by)", "1С8 УТ", "КИТ", "Кристалл"],
        answer: "Терминал МКП (mkp.patio-minsk.by)"
    },
    {
        q: "Когда осуществляется утилизация отходов бытовой техники из дома покупателя?",
        options: ["В момент доставки нового товара", "В любое время по согласованию", "Строго за день до доставки", "После проверки чека"],
        answer: "В момент доставки нового товара"
    },
    {
        q: "Как отображается номенклатура, требующая заполнения данных по индивидуальному учету (ИУ) в КИТ и УТ?",
        options: ["Жирным курсивом", "Красным цветом", "Подчеркнутым шрифтом", "Зачеркнутым текстом"],
        answer: "Жирным курсивом"
    },
    {
        q: "В какое время рабочего дня допускается распределять нераспределенный товар в розничной реализации?",
        options: ["В любое время рабочего дня", "Только строго перед закрытием смены", "Только в начале рабочего дня", "Только после согласования с Helpdesk"],
        answer: "В любое время рабочего дня"
    },
    {
        q: "Какой отчет рекомендуется открыть для проверки резервов и наличия товара при распределении розницы?",
        options: ["«Анализ доступности товаров на складах»", "«Ведомость по товарам на складах»", "«Отчет по остаткам и ценам»", "«Журнал проводок»"],
        answer: "«Анализ доступности товаров на складах»"
    },
    {
        q: "Какой номер необходимо скопировать из заказа КИМ для поиска предварительного чека в КИТ при закрытии неактуального заказа?",
        options: ["Номер BPM", "Номер РТиУ", "Номер штрихкода", "Артикул номенклатуры"],
        answer: "Номер BPM"
    },
    {
        q: "Что означает свободный остаток «-1» при формировании отчета анализа доступности?",
        options: ["Продали то, чего нет на остатках (пробито вручную)", "Товар зарезервирован другим магазином", "Сбой системы 1С", "Товар в пути"],
        answer: "Продали то, чего нет на остатках (пробито вручную)"
    },
    {
        q: "Какое качество автоматически проставляется в розничной реализации при пробитии товара по кассе без ПЧ?",
        options: ["Полка (Новый)", "Витрина", "Уцененный", "УНМ"],
        answer: "Полка (Новый)"
    }
];

// Вспомогательная функция перемешивания массивов (Fisher-Yates)
function shuffle(array) {
    let currentIndex = array.length, randomIndex;
    let newArray = [...array];
    while (currentIndex !== 0) {
        randomIndex = Math.floor(Math.random() * currentIndex);
        currentIndex--;
        [newArray[currentIndex], newArray[randomIndex]] = [newArray[randomIndex], newArray[currentIndex]];
    }
    return newArray;
}

let currentQuestions = [];
let userAnswers = []; // Сохраняет индекс выбранного ответа для каждого вопроса
let currentQuestionIndex = 0;
let timerInterval;
let timeLeft = 25 * 60;

function startTest() {
    document.getElementById('start-screen').classList.remove('active');
    document.getElementById('quiz-screen').classList.add('active');

    // Формируем уникальную выборку из 25 случайно перемешанных вопросов
    const shuffledTemplates = shuffle(baseTemplates);
    const selected = shuffledTemplates.slice(0, 25);

    currentQuestions = selected.map((item, idx) => {
        const shuffledOptions = shuffle(item.options);
        return {
            id: idx + 1,
            q: item.q,
            options: shuffledOptions,
            correctAnswer: item.answer
        };
    });

    userAnswers = new Array(currentQuestions.length).fill(null);
    currentQuestionIndex = 0;

    renderCurrentQuestion();
    startTimer();
}

function renderCurrentQuestion() {
    const container = document.getElementById('single-question-container');
    const q = currentQuestions[currentQuestionIndex];
    const selectedAnswerIndex = userAnswers[currentQuestionIndex];

    let optionsHTML = '';
    q.options.forEach((opt, optIndex) => {
        let labelClass = 'option-label';
        let statusIcon = '';
        let isChecked = selectedAnswerIndex === optIndex ? 'checked' : '';

        // Если ответ на этот вопрос УЖЕ был выбран
        if (selectedAnswerIndex !== null) {
            labelClass += ' disabled';
            if (opt === q.correctAnswer) {
                labelClass += ' correct';
                statusIcon = '<span class="status-icon">✓</span>';
            }
            if (selectedAnswerIndex === optIndex && opt !== q.correctAnswer) {
                labelClass += ' incorrect';
                statusIcon = '<span class="status-icon">✗</span>';
            }
        }

        optionsHTML += `
            <li class="option-item">
                <label class="${labelClass}" id="label_${optIndex}">
                    <div class="option-content">
                        <input type="radio" name="current_option" value="${optIndex}" ${isChecked} ${selectedAnswerIndex !== null ? 'disabled' : ''} onchange="selectOption(${optIndex})">
                        <span>${opt}</span>
                    </div>
                    ${statusIcon}
                </label>
            </li>
        `;
    });

    container.innerHTML = `
        <div class="question-card">
            <div class="question-header">
                <span>Вопрос ${currentQuestionIndex + 1} из ${currentQuestions.length}</span>
            </div>
            <div class="question-title">${q.q}</div>
            <ul class="options-list">${optionsHTML}</ul>
        </div>
    `;

    updateControls();
}

function selectOption(optIndex) {
    if (userAnswers[currentQuestionIndex] !== null) return; // Нельзя менять уже сделанный выбор

    userAnswers[currentQuestionIndex] = optIndex;
    renderCurrentQuestion(); // Перерисуем для отображения подсветки (зеленый/красный)
}

function updateControls() {
    // Обновляем прогресс-бар
    const progressPercent = ((currentQuestionIndex + 1) / currentQuestions.length) * 100;
    document.getElementById('progress').style.width = `${progressPercent}%`;

    // Состояние кнопок
    document.getElementById('btn-prev').disabled = currentQuestionIndex === 0;
    
    const nextBtn = document.getElementById('btn-next');
    const isAnswered = userAnswers[currentQuestionIndex] !== null;

    if (currentQuestionIndex === currentQuestions.length - 1) {
        nextBtn.innerText = 'Завершить тест';
        nextBtn.disabled = !isAnswered;
    } else {
        nextBtn.innerText = 'Далее';
        nextBtn.disabled = !isAnswered;
    }
}

function nextQuestion() {
    if (currentQuestionIndex < currentQuestions.length - 1) {
        currentQuestionIndex++;
        renderCurrentQuestion();
    } else {
        submitTest();
    }
}

function prevQuestion() {
    if (currentQuestionIndex > 0) {
        currentQuestionIndex--;
        renderCurrentQuestion();
    }
}

function startTimer() {
    timeLeft = 25 * 60;
    clearInterval(timerInterval);

    timerInterval = setInterval(() => {
        timeLeft--;
        let minutes = Math.floor(timeLeft / 60);
        let seconds = timeLeft % 60;

        document.getElementById('timer').innerText = 
            `${minutes < 10 ? '0' : ''}${minutes}:${seconds < 10 ? '0' : ''}${seconds}`;

        if (timeLeft <= 0) {
            clearInterval(timerInterval);
            alert('Время отведенное на тест истекло!');
            submitTest();
        }
    }, 1000);
}

function submitTest() {
    clearInterval(timerInterval);

    let correctCount = 0;
    let incorrectCount = 0;
    const reviewContainer = document.getElementById('review-container');
    reviewContainer.innerHTML = '';

    currentQuestions.forEach((q, index) => {
        const selectedOptIndex = userAnswers[index];
        const selectedText = selectedOptIndex !== null ? q.options[selectedOptIndex] : null;
        const isCorrect = selectedText === q.correctAnswer;

        if (isCorrect) {
            correctCount++;
        } else {
            incorrectCount++;
        }

        const reviewDiv = document.createElement('div');
        reviewDiv.className = `review-item ${isCorrect ? 'review-correct' : 'review-incorrect'}`;
        reviewDiv.innerHTML = `
            <strong>Вопрос ${index + 1}:</strong> ${q.q}<br>
            Ваш ответ: ${selectedText ? selectedText : '<i>Ответ не выбран</i>'}<br>
            ${!isCorrect ? `Правильный ответ: <b>${q.correctAnswer}</b>` : '<b>Верно!</b>'}
        `;
        reviewContainer.appendChild(reviewDiv);
    });

    const totalQuestions = currentQuestions.length;
    const percentage = Math.round((correctCount / totalQuestions) * 100);
    const isPassed = percentage >= 80;

    // Заполнение статистики
    document.getElementById('stat-total').innerText = totalQuestions;
    document.getElementById('stat-correct').innerText = correctCount;
    document.getElementById('stat-incorrect').innerText = incorrectCount;

    const statusTextElem = document.getElementById('result-status-text');
    statusTextElem.innerHTML = `<span style="color: ${isPassed ? 'var(--success)' : 'var(--danger)'}">` +
        `${isPassed ? 'ТЕСТ УСПЕШНО СДАН' : 'ТЕСТ НЕ СДАН'} (${percentage}%)</span>`;

    document.getElementById('quiz-screen').classList.remove('active');
    document.getElementById('result-screen').classList.add('active');
}

function restartTest() {
    document.getElementById('result-screen').classList.remove('active');
    document.getElementById('start-screen').classList.add('active');
    document.getElementById('timer').innerText = "25:00";
    document.getElementById('progress').style.width = "0%";
}
</script>

</body>
</html>
