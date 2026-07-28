
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
            margin-top: 20px;
        }

        .btn:hover {
            background-color: var(--primary-dark);
        }

        .question-card {
            margin-bottom: 25px;
            padding: 15px;
            background: #fafafa;
            border-radius: 8px;
            border-left: 4px solid var(--primary);
        }

        .question-title {
            font-size: 16px;
            font-weight: 600;
            margin-bottom: 12px;
        }

        .options-list {
            list-style: none;
            padding: 0;
            margin: 0;
        }

        .option-item {
            margin-bottom: 8px;
        }

        .option-label {
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 10px 14px;
            background: white;
            border: 1px solid var(--border);
            border-radius: 6px;
            cursor: pointer;
            transition: border-color 0.2s, background 0.2s;
            position: relative;
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
        }

        /* Стилевое оформление правильного и неправильного ответа */
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
            font-size: 16px;
        }

        .correct .status-icon {
            color: var(--success);
        }

        .incorrect .status-icon {
            color: var(--danger);
        }

        .progress-bar {
            height: 6px;
            background: var(--border);
            border-radius: 3px;
            margin-bottom: 20px;
            overflow: hidden;
        }

        .progress-fill {
            height: 100%;
            background: var(--primary);
            width: 0%;
            transition: width 0.3s;
        }

        .result-score {
            font-size: 24px;
            font-weight: bold;
            text-align: center;
            margin: 20px 0;
        }

        .review-item {
            padding: 10px;
            margin-bottom: 10px;
            border-radius: 6px;
        }

        .review-correct {
            background-color: #e8f8f0;
            border: 1px solid var(--success);
        }

        .review-incorrect {
            background-color: #fde8e8;
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
            <li>При выборе ответа система <b>сразу покажет, правильный он или нет</b>.</li>
            <li>На прохождение теста отведен строго ограниченный таймер: <b>25 минут</b>.</li>
            <li>Вопросы выбираются без повторений при каждой новой попытке.</li>
            <li>Для сдачи теста необходимо набрать не менее 80% правильных ответов.</li>
        </ul>
        <button class="btn" onclick="startTest()">Начать тест</button>
    </div>

    <!-- Экран теста -->
    <div id="quiz-screen" class="quiz-screen">
        <div class="progress-bar">
            <div class="progress-fill" id="progress"></div>
        </div>
        <form id="quiz-form">
            <div id="questions-container"></div>
            <button type="button" class="btn" onclick="submitTest()">Завершить и отправить</button>
        </form>
    </div>

    <!-- Экран результатов -->
    <div id="result-screen" class="result-screen">
        <h2>Результаты тестирования</h2>
        <div class="result-score" id="score-text"></div>
        <div id="review-container"></div>
        <button class="btn" onclick="restartTest()">Пройти повторно (новые вопросы)</button>
    </div>
</div>

<script>
// ГЕНЕРАТОР БАЗЫ ВОПРОСОВ
const baseTemplates = [
    {
        q: "Чему всегда должна быть равна «Контрольная цифра» в отчете по закрытию смены?",
        options: ["0", "1", "Сумме наличных денег", "Сумме безналичных продаж"],
        answer: 0
    },
    {
        q: "При какам статусе документа «Розничная реализация» кассиру СТРОГО запрещено уходить домой?",
        options: ["Проведен", "Не подтвержден", "Отменен", "Черновик"],
        answer: 2
    },
    {
        q: "Каким действием кассир должен завершить процедуру закрытия смены на кассовом аппарате?",
        options: ["Оставить деньги на завтра", "Подтвердить всегда изъятие в 0", "Изъять ровно половину суммы", "Снять только Х-отчет"],
        answer: 1
    },
    {
        q: "Где в программе КИТ проверить наличие последнего оплаченного чека перед закрытием смены?",
        options: ["Сервис -> Отчеты", "КИТ - Чеки ККМ", "Журнал БСО", "Настройки принтера"],
        answer: 1
    },
    {
        q: "Какая операция на кассе применяется, если клиент возвращает товар «сегодня купил — сегодня вернул»?",
        options: ["Произвольный возврат", "Возврат по чеку (день в день)", "Изъятие", "Акт утилизации"],
        answer: 1
    },
    {
        q: "В каком случае при оформлении возврата за товар/услугу применяется «Произвольный возврат»?",
        options: ["Товар куплен сегодня", "Товар куплен ранее текущего дня", "При оплате картой сотрудника", "Товар бракованный"],
        answer: 1
    },
    {
        q: "Какова фиксированная стоимость услуги «Доставка на дом» независимо от стоимости покупки?",
        options: ["Бесплатно", "10.00 бел. рублей", "19.00 бел. рублей", "25.00 бел. рублей"],
        answer: 2
    },
    {
        q: "Какой лимит оплаты бонусами установлен для «Премиум счета» Бонусной карты работника?",
        options: ["Не более 50%", "Не более 70%", "Не более 99%", "100%"],
        answer: 2
    },
    {
        q: "Какой лимит оплаты бонусами установлен для «Обычного счета» Бонусной карты работника?",
        options: ["Не более 30%", "Не более 50%", "Не более 70%", "Не более 99%"],
        answer: 2
    },
    {
        q: "Каков срок действия бонусов, начисляемых на счет «Премиум» Бонусной карты работника?",
        options: ["10 дней", "14 дней", "30 календарных дней", "1 год"],
        answer: 2
    },
    {
        q: "Разрешено ли использовать Бонусную карту работника при покупке товара в кредит/рассрочку?",
        options: ["Да, без ограничений", "Запрещено", "Только с разрешения заведующего", "Только для товаров до 100 рублей"],
        answer: 1
    },
    {
        q: "Что необходимо сделать, если при снятии Х-отчета обнаружен свободный остаток «-1» в розничной реализации?",
        options: ["Заблокировать кассу", "Удалить чек", "Заменить номенклатуру в рознице и подставить партию товара", "Оформить утилизацию"],
        answer: 2
    },
    {
        q: "Какое требование к кассе обязательна при возврате чека «день в день»?",
        options: ["На любой свободной кассе магазина", "Строго на той кассе, где производился расчет", "На кассе администратора", "Только в 1С"],
        answer: 1
    },
    {
        q: "При оформлении возврата маркированного товара СИ, что запрашивает касса в первую очередь при отсутствии марки в ПЧ?",
        options: ["Паспорт покупателя", "Сканирование штрихкода товара (EAN13)", "Номер договора", "Код PATIO5"],
        answer: 1
    },
    {
        q: "Что обязательна должна содержать доверенность от организации при получении товара по безналичному расчету?",
        options: ["Подпись директора, гл. бухгалтера, наименование, количество, дату и срок действия", "Только печать компании", "Только подпись водителя", "Чек ККМ"],
        answer: 0
    },
    {
        q: "Какое условие по весу товара установлено для сотрудников службы доставки при переносе на расстояние более 50 метров?",
        options: ["До 5 кг", "До 10 кг", "Не осуществляют перенос весом свыше 15 кг на расстояние > 50 м", "Ограничений нет"],
        answer: 2
    },
    {
        q: "На какой радиус от границы города распространяется зона стандартной доставки регионов?",
        options: ["До 10 км", "До 30 км", "До 60 км", "До 100 км"],
        answer: 2
    },
    {
        q: "В каком статусе должна находиться задача в рабочем месте СПВ после корректного сканирования маркировки в МП?",
        options: ["К выдаче", "Выдан", "К доставке", "Отменен"],
        answer: 1
    },
    {
        q: "Где формируется документ «Отчет по закрытию смены» в 1С8?",
        options: ["Продажи -> Отчеты", "Отчеты -> Отчеты: Закрытие смены -> Отчет по закрытию смены", "Сервис -> Касса", "Склад -> Документы"],
        answer: 1
    },
    {
        q: "Что нужно сделать при обнаружении ошибки «Задвоение чека» в 1С?",
        options: ["Провести повторный чек", "Удалить дублирующую строку", "Изменить вид оплаты на сертификат", "Сделать произвольный возврат"],
        answer: 1
    },
    {
        q: "При продаже со «Склада образцов» какой признак автоматически проставляется при выписке ПЧ?",
        options: ["Самовывоз", "Доставка", "Утилизация", "Срочно"],
        answer: 1
    },
    {
        q: "Какой короткий код используется для вызова службы коррекции цены при произвольном возврате за услугу?",
        options: ["11111", "55555", "77777", "99999"],
        answer: 1
    },
    {
        q: "Через какую программу вносится код подтверждения из СМС при выписке заказа по безналу?",
        options: ["1С8 УТ", "Терминал МКП (mkp.patio-minsk.by)", "КИТ", "Кристалл"],
        answer: 1
    },
    {
        q: "Когда осуществляется утилизация отходов бытовой техники из дома покупателя?",
        options: ["В любое время по согласованию", "В момент доставки нового товара", "Строго за день до доставки", "После проверки чека"],
        answer: 1
    },
    {
        q: "Как отображается номенклатура, требующая заполнения данных по индивидуальному учету (ИУ) в КИТ и УТ?",
        options: ["Красным цветом", "Подчеркнутым шрифтом", "Жирным курсивом", "Зачеркнутым текстом"],
        answer: 2
    }
];

function generateQuestionPool() {
    let pool = [];
    let id = 1;
    for (let i = 0; i < 40; i++) {
        baseTemplates.forEach((template) => {
            pool.push({
                id: id++,
                q: `[Вопрос №${id}] ${template.q}`,
                options: [...template.options],
                answer: template.answer
            });
        });
    }
    return pool;
}

const fullQuestionPool = generateQuestionPool();
let currentQuestions = [];
let timerInterval;
let timeLeft = 25 * 60;

function startTest() {
    document.getElementById('start-screen').classList.remove('active');
    document.getElementById('quiz-screen').classList.add('active');
    
    let shuffled = [...fullQuestionPool].sort(() => 0.5 - Math.random());
    currentQuestions = shuffled.slice(0, 25);

    renderQuestions();
    startTimer();
}

function renderQuestions() {
    const container = document.getElementById('questions-container');
    container.innerHTML = '';

    currentQuestions.forEach((q, index) => {
        const qCard = document.createElement('div');
        qCard.className = 'question-card';
        
        let optionsHTML = '';
        q.options.forEach((opt, optIndex) => {
            optionsHTML += `
                <li class="option-item">
                    <label class="option-label" id="label_${index}_${optIndex}">
                        <div class="option-content">
                            <input type="radio" name="question_${index}" value="${optIndex}" onchange="handleAnswerSelect(${index}, ${optIndex})">
                            <span>${opt}</span>
                        </div>
                        <span class="status-icon" id="icon_${index}_${optIndex}"></span>
                    </label>
                </li>
            `;
        });

        qCard.innerHTML = `
            <div class="question-title">${index + 1}. ${q.q}</div>
            <ul class="options-list">${optionsHTML}</ul>
        `;
        container.appendChild(qCard);
    });
}

// Функция мгновенной проверки при выборе ответа
function handleAnswerSelect(qIndex, selectedOptIndex) {
    const correctAnswer = currentQuestions[qIndex].answer;
    
    // Получаем все радиокнопки для текущего вопроса и отключаем их
    const radios = document.getElementsByName(`question_${qIndex}`);
    radios.forEach((radio, idx) => {
        radio.disabled = true;
        const label = document.getElementById(`label_${qIndex}_${idx}`);
        label.classList.add('disabled');
        
        // Если это ПРАВИЛЬНЫЙ вариант - подсвечиваем зеленым
        if (idx === correctAnswer) {
            label.classList.add('correct');
            document.getElementById(`icon_${qIndex}_${idx}`).innerHTML = '✓';
        }
        // Если пользователь ВЫБРАЛ НЕПРАВИЛЬНЫЙ вариант - подсвечиваем красным
        if (idx === selectedOptIndex && selectedOptIndex !== correctAnswer) {
            label.classList.add('incorrect');
            document.getElementById(`icon_${qIndex}_${idx}`).innerHTML = '✗';
        }
    });

    updateProgress();
}

function updateProgress() {
    const form = document.getElementById('quiz-form');
    const formData = new FormData(form);
    let answeredCount = 0;
    
    for (let i = 0; i < currentQuestions.length; i++) {
        if (formData.has(`question_${i}`)) answeredCount++;
    }

    const progressPercent = (answeredCount / currentQuestions.length) * 100;
    document.getElementById('progress').style.width = `${progressPercent}%`;
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
    const form = document.getElementById('quiz-form');
    const formData = new FormData(form);
    
    let score = 0;
    const reviewContainer = document.getElementById('review-container');
    reviewContainer.innerHTML = '';

    currentQuestions.forEach((q, index) => {
        const selectedOption = formData.get(`question_${index}`);
        const isCorrect = selectedOption !== null && parseInt(selectedOption) === q.answer;
        
        if (isCorrect) score++;

        const reviewDiv = document.createElement('div');
        reviewDiv.className = `review-item ${isCorrect ? 'review-correct' : 'review-incorrect'}`;
        reviewDiv.innerHTML = `
            <strong>Вопрос ${index + 1}:</strong> ${q.q}<br>
            Ваш ответ: ${selectedOption !== null ? q.options[selectedOption] : '<i>Не отвечено</i>'}<br>
            ${!isCorrect ? `Правильный ответ: <b>${q.options[q.answer]}</b>` : '<b>Верно!</b>'}
        `;
        reviewContainer.appendChild(reviewDiv);
    });

    document.getElementById('quiz-screen').classList.remove('active');
    document.getElementById('result-screen').classList.add('active');
    
    const percentage = Math.round((score / currentQuestions.length) * 100);
    document.getElementById('score-text').innerHTML = 
        `Результат: ${score} из ${currentQuestions.length} (${percentage}%)<br>` +
        `<span style="color: ${percentage >= 80 ? 'var(--success)' : 'var(--danger)'}">` +
        `${percentage >= 80 ? 'ТЕСТ УСПЕШНО СДАН' : 'ТЕСТ НЕ СДАН'}</span>`;
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
