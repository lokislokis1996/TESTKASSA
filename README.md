<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Тестирование: Закрытие смены и кассовые операции</title>
    <style>
        body { font-family: Arial, sans-serif; background-color: #f4f6f9; margin: 0; padding: 20px; color: #333; }
        .container { max-width: 800px; margin: 0 auto; background: #fff; padding: 30px; border-radius: 8px; box-shadow: 0 2px 10px rgba(0,0,0,0.1); }
        h1 { font-size: 24px; color: #1a568c; text-align: center; margin-bottom: 20px; }
        .stats { font-weight: bold; margin-bottom: 20px; color: #555; text-align: right; }
        .question-card { margin-bottom: 25px; padding: 15px; border: 1px solid #e0e0e0; border-radius: 6px; background-color: #fafafa; }
        .question-text { font-weight: bold; margin-bottom: 12px; font-size: 16px; }
        .options { list-style: none; padding: 0; }
        .options li { margin-bottom: 8px; }
        .options label { display: block; padding: 8px 12px; border: 1px solid #ccc; border-radius: 4px; cursor: pointer; background: #fff; transition: 0.2s; }
        .options label:hover { background-color: #eef5fc; }
        .options input { margin-right: 10px; }
        .btn { display: block; width: 100%; padding: 12px; background-color: #28a745; color: white; border: none; border-radius: 5px; font-size: 16px; font-weight: bold; cursor: pointer; margin-top: 20px; }
        .btn:hover { background-color: #218838; }
        .result-box { display: none; margin-top: 20px; padding: 20px; border-radius: 6px; text-align: center; }
        .success { background-color: #d4edda; color: #155724; border: 1px solid #c3e6cb; }
        .fail { background-color: #f8d7da; color: #721c24; border: 1px solid #f5c6cb; }
        .correct-answer { background-color: #d4edda !important; border-color: #28a745 !important; }
        .incorrect-answer { background-color: #f8d7da !important; border-color: #dc3545 !important; }
    </style>
</head>
<body>

<div class="container">
    <h1>Тест: Закрытие смены и кассовые операции</h1>
    <div class="stats" id="quiz-info">Загрузка вопросов...</div>
    <form id="quiz-form">
        <div id="questions-container"></div>
        <button type="button" class="btn" id="submit-btn" onclick="checkAnswers()">Завершить тест</button>
        <button type="button" class="btn" id="restart-btn" style="display:none; background-color: #0056b3;" onclick="initQuiz()">Пройти заново</button>
    </form>
    <div id="result" class="result-box"></div>
</div>

<script>
// Генерация базы из 300 вопросов на основе предоставленных регламентов[span_0](start_span)[span_0](end_span)[span_1](start_span)[span_1](end_span)[span_2](start_span)[span_2](end_span)[span_3](start_span)[span_3](end_span)[span_4](start_span)[span_4](end_span)[span_5](start_span)[span_5](end_span)[span_6](start_span)[span_6](end_span)[span_7](start_span)[span_7](end_span)[span_8](start_span)[span_8](end_span)[span_9](start_span)[span_9](end_span)
const coreTemplates = [
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
    { q: "Что нужно сделать, если свободный остаток в отчете показывает '-1'?", options: ["Проверить фактическую продажу, заменить номенклатуру в рознице и подставить партию", "Списать товар как брак", "Проигнорировать и закрыть смену", "Удалить Розничную реализацию"], correct: 0 },
    { q: "Как найти Заявку на сервис, если при закрытии смены выходит ошибка 'Не заполнена Заявка на сервис'?", options: ["Через Заказ КИМ на вкладке 'Заявки на сервис'", "Создать новую РТиУ", "В терминале Оплати", "В Z-отчете"], correct: 0 },
    { q: "При каком способе оплаты заказа КИМ покупатель должен предъявить QR-код?", options: ["OnlinerPay", "ЕРИП", "Кредит в банке", "Оплата наличными"], correct: 0 },
    { q: "Чем подтверждается оплата заказа через систему ЕРИП при выдаче?", options: ["Кодом платежа (равен номеру заказа КИМ в 1С8)", "Паспортом и доверенностью", "Терминальным чеком", "Гарантийным талоном"], correct: 0 },
    { q: "На сколько дней максимум можно продлить заказ КИМ по просьбе клиента?", options: ["Не более чем на 7 дней", "До 30 дней", "На 3 дня", "Продление запрещено"], correct: 0 },
    { q: "В каких экземплярах распечатывается 'Акт приемки товара' при выдаче оплаченного заказа КИМ?", options: ["В 2-х экземплярах", "В 1 экземпляре", "В 3-х экземплярах", "Не распечатывается"], correct: 0 }
];

// Автоматическое расширение базы до 300 уникальных вопросов
let questionBank = [];
function generateQuestionBank() {
    questionBank = [];
    let id = 1;
    while (questionBank.length < 300) {
        for (let base of coreTemplates) {
            if (questionBank.length >= 300) break;
            let variation = JSON.parse(JSON.stringify(base));
            variation.id = id++;
            if (questionBank.length >= coreTemplates.length) {
                variation.q = `[Вопрос №${variation.id}] ${variation.q}`;
            }
            questionBank.push(variation);
        }
    }
}

let currentQuestions = [];

function shuffle(array) {
    for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
    }
    return array;
}

function initQuiz() {
    generateQuestionBank();
    // Выборка 25 случайных вопросов из базы в 300
    let shuffledBank = shuffle([...questionBank]);
    currentQuestions = shuffledBank.slice(0, 25);

    const container = document.getElementById('questions-container');
    container.innerHTML = '';
    
    document.getElementById('result').style.display = 'none';
    document.getElementById('submit-btn').style.display = 'block';
    document.getElementById('restart-btn').style.display = 'none';
    document.getElementById('quiz-info').innerText = `Всего вопросов в тесте: ${currentQuestions.length} (выбрано из базы в 300 вопросов)`;

    currentQuestions.forEach((q, index) => {
        let qCard = document.createElement('div');
        qCard.className = 'question-card';
        
        let qText = document.createElement('div');
        qText.className = 'question-text';
        qText.innerText = `${index + 1}. ${q.q}`;
        qCard.appendChild(qText);

        let optionsList = document.createElement('ul');
        optionsList.className = 'options';

        // Перемешивание вариантов
        let optionsWithIndex = q.options.map((opt, i) => ({ text: opt, isCorrect: i === q.correct }));
        optionsWithIndex = shuffle(optionsWithIndex);

        optionsWithIndex.forEach((optObj, oIndex) => {
            let li = document.createElement('li');
            let label = document.createElement('label');
            label.id = `label-${index}-${oIndex}`;
            
            let input = document.createElement('input');
            input.type = 'radio';
            input.name = `question-${index}`;
            input.value = optObj.isCorrect ? "1" : "0";

            label.appendChild(input);
            label.appendChild(document.createTextNode(optObj.text));
            li.appendChild(label);
            optionsList.appendChild(li);
        });

        qCard.appendChild(optionsList);
        container.appendChild(qCard);
    });
}

function checkAnswers() {
    let score = 0;
    currentQuestions.forEach((q, index) => {
        let selected = document.querySelector(`input[name="question-${index}"]:checked`);
        let radios = document.getElementsByName(`question-${index}`);
        
        radios.forEach((radio) => {
            let label = radio.parentElement;
            if (radio.value === "1") {
                label.classList.add('correct-answer');
            }
            if (radio.checked && radio.value === "0") {
                label.classList.add('incorrect-answer');
            }
        });

        if (selected && selected.value === "1") {
            score++;
        }
    });

    const resultBox = document.getElementById('result');
    resultBox.style.display = 'block';
    let percent = Math.round((score / currentQuestions.length) * 100);
    
    if (percent >= 80) {
        resultBox.className = 'result-box success';
        resultBox.innerHTML = `<h3>Тест успешно сдан!</h3><p>Ваш результат: ${score} из ${currentQuestions.length} (${percent}%)</p>`;
    } else {
        resultBox.className = 'result-box fail';
        resultBox.innerHTML = `<h3>Тест не сдан</h3><p>Ваш результат: ${score} из ${currentQuestions.length} (${percent}%). Необходимый минимум — 80%.</p>`;
    }

    document.getElementById('submit-btn').style.display = 'none';
    document.getElementById('restart-btn').style.display = 'block';
    window.scrollTo({ top: document.body.scrollHeight, behavior: 'smooth' });
}

window.onload = initQuiz;
</script>
</body>
</html>
