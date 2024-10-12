<script setup>
import {computed, onMounted, onUnmounted, ref, watch} from "vue";
import axios from "axios";
import {
  Button as AButton,
  Col as ACol,
  ConfigProvider as AConfigProvider,
  Form as AForm,
  FormItem as AFormItem,
  Textarea as ATextarea,
  Row as ARow,
  Select as ASelect,
  Table as ATable,
  Tag as ATag,
  Badge as ABadge,
  Image as AImage,
  Space as ASpace,
  message,
} from "ant-design-vue";
import ruRu from "ant-design-vue/es/locale/ru_RU";
import dayjs from "dayjs";
import ru from "dayjs/locale/ru";
import utc from "dayjs/plugin/utc";

dayjs.locale(ru);
dayjs.extend(utc);

const companyArray = [
  {
    id: 1,
    name: "ARANTA Decor (WB)",
    apiToken: "eyJhbGciOiJFUzI1NiIsImtpZCI6IjIwMjQwOTA0djEiLCJ0eXAiOiJKV1QifQ.eyJlbnQiOjEsImV4cCI6MTc0MzUzODUyMSwiaWQiOiIwMTkyNDcyNS1mMGUzLTc4MTEtYTJjZC1jOThiOWZlNDc0YjMiLCJpaWQiOjUzNzE1MjI2LCJvaWQiOjU3MDAwOCwicyI6MTI4LCJzaWQiOiI0ZTkzM2M1NC1kYzA2LTQzOWEtYTA2Ni1mNWRkZjFlMDdhNDUiLCJ0IjpmYWxzZSwidWlkIjo1MzcxNTIyNn0.dWCg4v_7MCRQGNMEXuKTM910gDu0ZuijqaGiHvGE4mQuJibGXX4S5qW5csKJQr3bKnZsqDN3N50x1VMLaFYzBw",
    telegramToken: "7954264530:AAFBQFwNmh8ZnL7CQXwZrjLJuBjlAkgdeKg",
    chatId: "514186798",
    prompt: "Мы компания ARANTA Decor, занимаемся продажей Сухоцветов (пампасной травы), как натуральных, стабилизированных, так и искусственных. Так же в нашем ассортименте есть ароматические саше и вазы для цветов и сухоцветов. Ты являешься менеджером, который отвечает на отзывы и вопросы покупателей. Отвечая на отзывы и вопросы, клиент должен чувствовать доброту и позитивные эмоции от ответов на их отзывы и вопросы. Ответ должен быть коротким, оригинальным, неформальным, с приятным послевкусием. Можно добавить приятных смайликов, что бы текст выглядел живее. В конце ответа должна быть фраза от нашей команды, а в начале приветствие. Также должен быть призыв к покупке снова. Отзыв который я пришлю будет содержать имя покупателя, оценку от покупателя, комментарии, достоинства товара и недостатки. Если клиент просто поставил низкую оценку без дополнительной информации, нужно поинтересоваться где мы не доработали. Если клиент получил негативный опыт при взаимодействии с нашим товаром, нужно обязательно попросить его связаться с нами через чат продавца и заверить что мы решим его вопрос и не оставим его с проблемой. Если клиент жалуется на доставку, нужно объяснить что мы не можем влиять на скорость и качество доставки, за это отвечает маркетплейс, но всё же посоветовать связаться с нами для решения вопроса. Сейчас я пришлю тебе отзыв от покупателя и тебе надо ответить.",
  },
  {
    id: 2,
    name: "ARANTA Art Supplies (WB)",
    apiToken: "eyJhbGciOiJFUzI1NiIsImtpZCI6IjIwMjQxMDAxdjEiLCJ0eXAiOiJKV1QifQ.eyJlbnQiOjEsImV4cCI6MTc0NDQ3MjE0MiwiaWQiOiIwMTkyN2VjYi1kYWZiLTcxNDItOWJjZC03MDA0YmMzZTUxYzYiLCJpaWQiOjk2OTgyNDY4LCJvaWQiOjQwMTg1MzQsInMiOjEyOCwic2lkIjoiZGNlM2E3NDktZTRmZC00OTAwLWJiZjItYmMzNjI4M2Q5OTgwIiwidCI6ZmFsc2UsInVpZCI6OTY5ODI0Njh9.e5n8WrYSylkgacUML2a4E8RfHaK1QZQ5ijWg912ePuUjerovDIeZFhkotg5ZvOwqDixuPaxJr9_BM23AlXcqDg",
    telegramToken: "7954264530:AAFBQFwNmh8ZnL7CQXwZrjLJuBjlAkgdeKg",
    chatId: "514186798",
    prompt: ""
  },
  {
    id: 3,
    name: "Sunflowers (WB)",
    apiToken: "eyJhbGciOiJFUzI1NiIsImtpZCI6IjIwMjQxMDAxdjEiLCJ0eXAiOiJKV1QifQ.eyJlbnQiOjEsImV4cCI6MTc0NDQ3MjI4NiwiaWQiOiIwMTkyN2VjZS0xMDc4LTc5YWMtYWRhOS02OGEyY2E3ZTFhZDIiLCJpaWQiOjk2OTgyNDY4LCJvaWQiOjQwNzg0NjMsInMiOjEyOCwic2lkIjoiMGYzNjQ2MDMtYTgxYy00M2FkLTkyOWItMmFmMzE5YWFlNzNjIiwidCI6ZmFsc2UsInVpZCI6OTY5ODI0Njh9.zKFH5qObtzMhttQ6Ju0XBuBmLbrjofW7ilXR8dqskWSVyijpf6dOQ2qJxW9Umzw35rtPDAwrc-caUuHkGkeHKg",
    telegramToken: "7954264530:AAFBQFwNmh8ZnL7CQXwZrjLJuBjlAkgdeKg",
    chatId: "514186798",
    prompt: ""
  },
];

const telegramChatIds = [
  {
    name: "Александр",
    id: 514186798,
  },
  {
    name: "Артём",
    id: 428444661,
  }
];

let timerId = null;
const isRunning = ref(false);

const columns =  ref([
  {
    title: 'Дата',
    dataIndex: 'createdDate',
    key: 'createdDate',
    width: '8%',
  },
  {
    title: 'Имя',
    dataIndex: 'userName',
    key: 'userName',
    width: '10%',
  },
  {
    title: 'Фотографии',
    dataIndex: 'photoLinks',
    key: 'photoLinks',
    width: '10%',
  },
  {
    title: 'Отзыв',
    dataIndex: 'comment',
    key: 'comment',
    width: '25%',
  },
  {
    title: 'Оценка',
    dataIndex: 'productValuation',
    key: 'productValuation',
    width: '10%'
  },
  {
    title: 'Ответ',
    dataIndex: 'answer',
    key: 'answer',
    width: '18%',
  },
  {
    title: 'Статус',
    key: 'status',
    dataIndex: 'status',
    width: '8%',
  },
  {
    title: 'Действие',
    key: 'makeAnswer',
    width: '6%',
  },
]);

const transformedCompanyOptions = computed(() => {
  return companyArray.map(company => ({
    label: company.name,
    value: JSON.stringify(company)
  }));
});

const loading = ref(false);

const companyOptions = ref([]);

const companySelected = ref(JSON.stringify(companyArray[0]));

watch(companySelected, (newValue, oldValue) => {
  if (newValue !== oldValue) {
    handleStop();

    initValues();
  }
});

companyOptions.value = transformedCompanyOptions.value;

const transformedCompanySelected = computed(() => JSON.parse(companySelected.value));

const feedbacksList = ref([]);
const feedbacksData = ref([]);

const countUnanswered = computed(() => {
  let count = 0;

  feedbacksData.value.forEach((feedback) => {
    if (!feedback.status) {
      count++;
    }
  });

  return count;
});

const OPENAI_API_KEY = ref("");

watch(OPENAI_API_KEY, (newValue) => {
  localStorage.setItem(fieldCompanies("OPENAI_API_KEY"), newValue);
})

const prompt = ref("");

function fieldCompanies(fieldName) {
  return `${transformedCompanySelected.value.name.replaceAll(" ", "")}_${fieldName}`
}

watch(prompt, (newValue) => {
  localStorage.setItem(fieldCompanies("prompt"), newValue);
});

function initValues() {
  const getPrompt = localStorage.getItem(fieldCompanies("prompt"));
  prompt.value = getPrompt || transformedCompanySelected.value.prompt;

  const getOPENAI_API_KEY = localStorage.getItem(fieldCompanies("OPENAI_API_KEY"));
  OPENAI_API_KEY.value = getOPENAI_API_KEY || "";
}

onMounted(() => {
  initValues();
});

async function startGenerateAnwser(id) {
  const feedback = feedbacksData.value.find((feedbackItem) => feedbackItem.id === id);

  feedback.answer = await generateAnwser({
    userName: feedback.userName,
    comment: feedback.comment,
    productName: feedback.productName,
    productValuation: feedback.productValuation,
  });
}

async function generateAnwser(options) {
  const { userName, comment, productName, productValuation } = options;

  const client = axios.create({
    headers: {
      Authorization: `Bearer ${OPENAI_API_KEY.value}`,
    }
  });

  const params = {
    // model: "gpt-3.5-turbo",
    model: "gpt-4o",
    messages: [
      {
        role: "user",
        content: `${prompt.value}. Вот информация о его отзыве: Имя покупателя: ${userName}, товар: ${productName}, оценка: ${productValuation}, отзыв: ${comment.text}, достоинства товара по мнению покупателя: ${comment.pros}, недостатки товара по мнению покупателя: ${comment.cons}.`,
      }
    ],
    max_tokens: 1000
  };

  message.loading('Генерация ответа');

  try {
    const response = await client.post("https://api.openai.com/v1/chat/completions", params);

    return response.data.choices[0].message.content; // Возвращаем ответ
  } catch (error) {
    console.error("Ошибка при запросе к OpenAI:", error);
    return "Не удалось получить ответ от OpenAI"; // Возвращаем сообщение об ошибке
  }
}

watch(feedbacksList, async (newData) => {
  const updateData = ref([]);

  for (const newItem of newData) {
    const existingItem = feedbacksData.value.find(item => item.id === newItem.id);

    if (existingItem) {
      updateData.value.push(existingItem);
    } else {
      // Ждем ответа от OpenAI
      // const answer = await generateAnwser({
      //   userName: newItem.userName,
      //   comment: newItem.comment,
      //   productName: newItem.productName,
      //   productValuation: newItem.productValuation,
      // });

      // Логируем ответ для проверки
      // console.log("Сгенерированный ответ для отзыва:", answer);

      // Добавляем элемент в массив только после получения ответа
      // updateData.value.push({
      //   ...newItem,
      //   status: false,
      //   answer: answer, // Убедитесь, что сюда добавляется правильный ответ
      // });

      updateData.value.push({
        ...newItem,
        status: false,
        answer: "Ответ еще не сгенерирован"
      });

      // sendMessageToTelegram({
      //   createdDate: newItem.createdDate,
      //   userName: newItem.userName,
      // })
      sendMessageToAllUsers(`
*${transformedCompanySelected.value.name}*
Новый отзыв от *${newItem.userName ? newItem.userName : 'Нет имени'}*
SKU *${newItem.comment.supplierArticle}*
Оценка *${getScoreWithSymbol(newItem.productValuation)}*
Дата *${newItem.createdDate}*
`);
    }
  }

  feedbacksData.value = updateData.value;
});

function getScoreWithSymbol(value) {
  if (value === 5) return `${value} 💚`;
  else if (value === 4) return `${value} 💛`;
  else return `${value} 💔`;
}

function feedbacksGet() {
  loading.value = true;
  message.loading('Загрузка отзывов', 0.5);

  axios
    .get("https://feedbacks-api.wildberries.ru/api/v1/feedbacks", {
      params: {
        isAnswered: false,
        take: 5000,
        skip: 0,
        order: "dateAsc",
      },
      headers: {
        "Authorization": `${transformedCompanySelected.value.apiToken}`
      }
    })
    .then(response => {
      feedbacksList.value = response.data.data.feedbacks.map((feedback) => ({
        id: feedback.id,
        createdDate: dayjs(feedback.createdDate).format('DD.MM.YYYY HH:mm'),
        userName: feedback.userName,
        comment: {
          supplierArticle: feedback.productDetails.supplierArticle,
          pros: feedback.pros,
          cons: feedback.cons,
          text: feedback.text,
        },
        productName: feedback.productDetails.productName,
        productValuation: feedback.productValuation,
        photoLinks: feedback.photoLinks,
      }));

      loading.value = false;
      // countUnanswered.value = response.data.data.countUnanswered;
    })
    .catch(error => {
      console.log(error);
      message.error('Ошибка при загрузке отзывов!');
      loading.value = false;
    });
}

function handleStart () {
  isRunning.value = true;
  feedbacksGet();
  timerId = setInterval(feedbacksGet, 600000); // Вызывать функцию каждые 10 минут
}

function handleStop () {
  isRunning.value = false;
  clearInterval(timerId); // Остановить таймер
}

function resetTimer() {
  clearInterval(timerId); // Останавливаем текущий таймер
  timerId = setInterval(feedbacksGet, 600000); // Запускаем новый таймер
}

onUnmounted(() => {
  handleStop();
});

function makeAnswer(id) {
  const feedback = feedbacksData.value.find((feedbackItem) => feedbackItem.id === id);

  if (feedback) {
    resetTimer();
    axios
      .patch("https://feedbacks-api.wildberries.ru/api/v1/feedbacks", {
        id: feedback.id,
        text: feedback.answer
      }, {
        headers: {
          Authorization: `${transformedCompanySelected.value.apiToken}`
        }
      })
      .then(() => {
        feedback.status = true;
        message.success('Ответ успешно отправлен!');
      })
      .catch((error) => {
        console.error(error);
        message.error('Ошибка при отправке ответа!');
      });
  }
}

function isValidArray(arr) {
  return arr !== null && Array.isArray(arr) && arr.length > 0;
}

// const TELEGRAM_API_URL = 'https://api.telegram.org/bot';
const token = transformedCompanySelected.value.telegramToken;

// Функция для получения обновлений и вытаскивания chat_id пользователей
// async function getChatIds() {
//   try {
//     const response = await axios.get(`https://api.telegram.org/bot${token}/getUpdates`);
//     const updates = response.data.result;
//
//     // Извлекаем уникальные chat_id
//     const chatIds = [...new Set(updates.map(update => update.message.chat.id))];
//
//     return chatIds;
//   } catch (error) {
//     console.error('Ошибка при получении обновлений:', error);
//   }
// }

// Функция для отправки сообщения всем пользователям
async function sendMessageToAllUsers(message) {
  // const chatIds = await getChatIds();

  if (telegramChatIds.length === 0) {
    console.log('Нет новых пользователей.');
    return;
  }

  for (const chatId of telegramChatIds) {
    try {
      await axios.post(`https://api.telegram.org/bot${token}/sendMessage`, {
        chat_id: chatId.id,
        text: message,
        parse_mode: 'Markdown'
      });
      // console.log(`Сообщение отправлено пользователю с chat_id: ${chatId}`);
    } catch (error) {
      console.error(`Ошибка при отправке сообщения пользователю с chat_id: ${chatId.id}`, error);
    }
  }
}

// let users = []; // Список пользователей с флагом отправки сообщений
// let lastUpdateId = 0; // Хранение последнего обработанного обновления

// Получение обновлений
// async function getUpdates() {
//   try {
//     const response = await axios.get(`${TELEGRAM_API_URL}${TOKEN}/getUpdates`, {
//       params: {
//         offset: lastUpdateId + 1, // Игнорируем все обновления до последнего обработанного
//       }
//     });
//     const updates = response.data.result;
//
//     updates.forEach(update => {
//       if (update.message && update.message.from) {
//         const userID = update.message.from.id;
//
//         // Проверяем, есть ли пользователь в списке
//         const userExists = users.find(user => user.id === userID);
//         if (!userExists) {
//           // Добавляем нового пользователя с флагом, что сообщение ещё не отправлено
//           users.push({ id: userID, messageSent: false });
//           // console.log(`Добавлен новый пользователь с ID: ${userID}`);
//         }
//
//         // Обновляем `lastUpdateId` на максимальное значение
//         if (update.update_id > lastUpdateId) {
//           lastUpdateId = update.update_id;
//         }
//       }
//     });
//   } catch (error) {
//     console.error('Ошибка при получении обновлений:', error);
//   }
// }
//
// // Отправка сообщений только тем, кому ещё не отправлено
// async function sendMessageToAllUsers(message) {
//   try {
//     for (const user of users) {
//       // Отправляем сообщение только если оно ещё не было отправлено
//       if (!user.messageSent) {
//         console.log('Отправка: ', user.id);
//
//         await axios.post(`${TELEGRAM_API_URL}${TOKEN}/sendMessage`, {
//           chat_id: user.id,
//           text: message,
//           parse_mode: 'Markdown'
//         });
//
//         console.log(`Сообщение отправлено пользователю с ID: ${user.id}`);
//         user.messageSent = true; // Отмечаем, что сообщение было отправлено
//       }
//     }
//   } catch (error) {
//     console.error('Ошибка при отправке сообщения:', error);
//   }
// }

// Основная асинхронная функция
// async function main(message) {
//   await getUpdates(); // Дожидаемся выполнения getUpdates
//   await sendMessageToAllUsers(message); // После этого отправляем сообщение
// }

// Вызов основной функции
// main();

function getColorProductValuation(record) {
  if (record.productValuation === 5) {
    return "#87d068";
  } else if (record.productValuation === 4) {
    return "gold";
  } else if (record.productValuation <= 3) {
    return "red";
  }
}

// Храним информацию о текущей редактируемой строке
const editingRow = ref({ id: null, answer: '' });

// Проверяем, редактируется ли строка
const isEditing = (id) => {
  return editingRow.value.id === id;
};

// Функция для редактирования
const edit = (record) => {
  // Инициализируем ответ текущим значением
  editingRow.value = { id: record.id, answer: record.answer || '' };
};

// Сохранение изменений
const save = (id) => {
  const index = feedbacksData.value.findIndex((item) => item.id === id);
  if (index !== -1) {
    feedbacksData.value[index].answer = editingRow.value.answer;
  }
  editingRow.value = { id: null, answer: '' };
};

// Отмена редактирования
const cancelEdit = () => {
  editingRow.value = { id: null, answer: '' };
};
</script>

<template>
  <a-config-provider :locale="ruRu">
    <a-form ref="form" layout="vertical">
      <a-row :gutter="24">
        <a-col :span="24">
          <a-form-item label="OPENAI_API_KEY" name="OPENAI_API_KEY">
            <a-textarea
              v-model:value="OPENAI_API_KEY"
              auto-size
              :disabled="isRunning"
            />
          </a-form-item>
        </a-col>
      </a-row>

      <a-row :gutter="24">
        <a-col :span="24">
          <a-form-item label="Prompt" name="prompt">
            <a-textarea
              v-model:value="prompt"
              auto-size
              :disabled="isRunning"
            />
          </a-form-item>
        </a-col>
      </a-row>

      <a-row :gutter="24" style="display: flex; align-items: flex-end;">
        <a-col :span="12">
          <a-form-item label="Компания" name="companySelected">
            <a-select
              v-model:value="companySelected"
              :options="companyOptions"
              placeholder="Выберите из списка"
              show-search
            />
          </a-form-item>
        </a-col>

        <a-col :span="3">
          <a-form-item>
            <a-button
              type="primary"
              html-type="submit"
              :disabled="isRunning"
              @click="handleStart"
              block
            >
              Запустить
            </a-button>
          </a-form-item>
        </a-col>

        <a-col :span="3">
          <a-form-item>
            <a-button
              html-type="submit"
              :disabled="!isRunning"
              @click="handleStop"
              block
            >
              Остановить
            </a-button>
          </a-form-item>
        </a-col>
      </a-row>
    </a-form>

    <div style="display: flex; align-items: flex-end; margin-bottom: 10px;">
      <span style="margin-right: 5px;">Отзывы на которые не ответили:</span><a-badge :count="countUnanswered" show-zero/>
    </div>

    <a-table
      :columns="columns"
      :data-source="feedbacksData"
      :loading="loading"
      row-key="id"
    >
      <template #bodyCell="{ column, record, index }">
        <template v-if="column.key === 'userName'">
          <span v-if="record.userName">
            {{ record.userName }}
          </span>
          <a-tag v-else color="red">
            Нет имени
          </a-tag>
        </template>

        <template v-if="column.key === 'photoLinks'">
          <div
            v-if="isValidArray(record.photoLinks)"
            v-for="image in record.photoLinks"
            class="image__list-item"
          >
            <a-image
              :height="140"
              :src="image.miniSize"
              :preview="false"
            />
          </div>

          <span v-else>
            <a-tag color="red">
             Отсутствуют
            </a-tag>
          </span>
        </template>

        <template v-if="column.key === 'productValuation'">
          <a-tag :color="getColorProductValuation(record)">
            <span v-if="record.productValuation === 5">
              Отлично ({{ record.productValuation }})
            </span>
            <span v-if="record.productValuation === 4">
              Хорошо ({{ record.productValuation }})
            </span>
            <span v-if="record.productValuation === 3">
              Удовлет. ({{ record.productValuation }})
            </span>
            <span v-if="record.productValuation === 2">
              Плохо ({{ record.productValuation }})
            </span>
            <span v-if="record.productValuation === 1">
              Ужасно ({{ record.productValuation }})
            </span>
          </a-tag>
        </template>

        <template v-if="column.key === 'comment'">
          <div style="display: flex; flex-direction: column;">
            <p v-if="record.comment.supplierArticle">
              <b>SKU: </b> {{ record.comment.supplierArticle }}
            </p>
            <p v-if="record.comment.pros">
              <b>Достоинства: </b> {{ record.comment.pros }}
            </p>
            <p v-if="record.comment.cons">
              <b>Недостатки: </b> {{ record.comment.cons }}
            </p>
            <p v-if="record.comment.text">
              {{ record.comment.text }}
            </p>
          </div>
        </template>

        <template v-if="column.key === 'answer'">
          <template v-if="isEditing(record.id)">
            <a-textarea
              v-model:value="editingRow.answer"
              auto-size
            />
            <a-space style="margin-top: 10px;">
              <a-button @click="save(record.id)" type="primary">
                Сохранить
              </a-button>
              <a-button @click="cancelEdit">
                Отмена
              </a-button>
            </a-space>
          </template>
          <template v-else>
            <span @click="edit(record)"> <!-- При клике переходим в режим редактирования -->
              {{ record.answer || 'Нет ответа' }}
            </span>
          </template>
        </template>

        <template v-if="column.key === 'status'">
          <a-tag
            :color="record.status ? 'green' : 'volcano'"
          >
            {{ record.status ? 'Ответили' : 'Не ответили' }}
          </a-tag>
        </template>

        <template v-if="column.key === 'makeAnswer'">
          <div style="display: flex; flex-direction: column">
            <a @click="startGenerateAnwser(record.id)" style="margin-bottom: 10px">Сгенерировать</a>

            <a @click="makeAnswer(record.id)">Ответить</a>
          </div>
        </template>
      </template>
    </a-table>
  </a-config-provider>
</template>

<style scoped>
.image__list-item {
  margin-bottom: 5px;
}

.image__list-item:last-child {
  margin-bottom: 0;
}
</style>
