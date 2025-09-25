---
title: 디스코드 일정관리 챗봇 개발기
categories: [Project, Discord]
tags: [chatbot, daily, check, discord, Discord, 디스코드, 챗봇, 개발]
description: >-
    개인 일정 관리 챗봇 만들기 (로컬 실행)
---

## 디스코드 일정 리마인드 챗봇 개발기
기존에는 일정을 공책에 작성하거나 메모지에 작성해 모니터에 붙여두었다. 하지만 이 방식은 내가 어디에 기록을 했는지 찾아야 하고 메모지 같은 경우에는 접착력이 약해 스스로 떨어져 사라지는 문제가 있었다. 특히 일정이 반복되거나 중간에 일정이 변경되어서 나중에 해야 할 작업은 내가 어디에 작성해두었는지 기억하지 못하는 경우가 많았다. 이를 개선하기 위해 매일 아침 일정 정보를 리마인드 해주는 챗봇을 개발하기로 결정했다.

## 플랫폼 선정
챗봇을 구현할 수 있는 다양한 플랫폼 (슬랙, 잔디, 지라 등등) 중 Discord를 선택했다. 이유는 아래와 같다.
- 주요 기능이 모두 무료
- 사용자 친화적인 UI
- 대학 시절부터 사용해서 익숙함

<hr/>

## 개발 과정
### 1. 디스코드 개발자 모드 활성화

![개발자_모드](/assets/img/post_img/discord/devlopment_mode.png)

개발자 모드를 활성화하면 사용자, 서버, 메세지의 ID를 복사할 수 있다. 이 ID는 디스코드 내에서 고유값의 역할을 하기 때문에 ID만으로 누가 어느 서버에서 메세지를 보냈는지 확인할 수 있다.

### 2. [디스코드 개발자 사이트](https://discord.com/developers/applications)에서 앱 등록하기

![디스코드_개발자_사이트](/assets/img/post_img/discord/dev.png)

위 이미지에서 파란색 박스로 표시된 **New Application**을 클릭하면 새로은 앱을 생성한다.

![앱_생성](/assets/img/post_img/discord/create_app.png)

애플리케이션 이름을 입력하고 Create를 클릭하면 등록이 완료된다. 참고로, 개발자 정책을 동의하지 않으면 등록되지 않으니 무조건 체크 박스를 클릭해야 한다.

### 3. Bot 설정하기

![생성_후](/assets/img/post_img/discord/create.png)

애플리케이션을 생성하면 왼쪽에 대시보드가 새롭게 열린다. 여기서 우리가 봇 세팅을 위해 확인해야 하는 것은 Bot과 OAuth2이다. 우선 Bot 항목부터 선택한다.

![Bot](/assets/img/post_img/discord/reset_token.png)

**Reset Token**을 클릭하여 토큰을 새로 발급받는다. 이 토큰은 봇과 코드 간 인증에 사용되므로 안전한 위치에 저장해두어야 한다. 참고로 시크릿 브라우저 모드에서는 토큰 생성 시도 시 `입력 속도를 제한 중이에요.` 알림이 뜨고 생성이 되지 않으므로 기본 모드에서 진행해야 한다.

![Bot](/assets/img/post_img/discord/bot.png)

그리고 봇을 초대하려면 초대 링크를 생성해야 함. 이 링크는 OAuth2 탭에서 생성 가능함.
봇 설정 하단의 **Privileged Gateway Intents** 항목은 3개 모두 활성화해야 한다.
- **Presence Intent** : 사용자의 상태 변화 (온라인 / 오프라인 등)을 봇이 감지할 수 있도록 권한을 부여한다.
- **Server Members Intent** : 봇이 서버 멤버에 대한 정보 (ef.멤버가 서버에 들어옴 / 멤버가 서버에서 나감 / 업데이트 된 멤버의 정보 등)에 접근할 수 있도록 허용한다.
- **Message Content Intent** : 봇이 메세지 내용을 읽을 수 있도록 허용한다.
변경 후 **Save Changes** 버튼을 눌러 내용을 저장한다. 이렇게 봇 설정이 끝났다. 이 봇을 내 서버에 초대하려면 초대 링크를 생성해야 한다. 초대 링크는 OAuth2 탭에서 생성할 수 있다.

### 4. 초대 링크 생성하기

![OAuth2](/assets/img/post_img/discord/oauth.png)

OAuth2 탭에서 URL Generator를 통해 초대 링크를 생성한다. 우리는 봇을 만들었기 때문에 SCOPES는 BOT을 선택하면 된다.

![OAuth2](/assets/img/post_img/discord/permissions.png)

BOT PERMISSIONS에서는 봇의 권한을 설정한다. 내가 만든 봇은 개인용 봇이기 때문에 모든 권한을 부여할 수 있는 ADMINSTRATOR 으로 설정했다.

![OAuth2](/assets/img/post_img/discord/url.png)

권한 설정 후 아래로 내려가면 초대 url을 확인할 수 있다. 참고로 Integration Type에는 다음 두 가지가 있으며 나는 Guild Install을 선택했다.
- Guild Install : 특정 서버에 애플리케이션을 설치한다.
- User Install : 애플리케이션을 자신의 계정에 개별적으로 설치한다.

## 개발
디스코드는 다양한 언어로 봇을 만들 수 있는데, 이는 공식적으로 제공되는 것이 아닌 사용자들이 만든 API로 만든다. 현재 제공되는 API는 파이썬 / Node.js / JAVA / C# / Go / C++ 이 있다. 나는 서버 개발 시 사용해 본 경험이 있는 Node.js를 사용해 봇을 만들기로 결정했다.

### 1. 환경 세팅

- 언어 : JavaScript
- 프레임워크 : Node v22.12.0
- 편집기 : Visual Studio Code

1. 봇 개발을 위한 폴더를 생성하고 vscode로 연다.
2. 터미널에서 node 세팅을 위해 `npm init`을 입력한다.
3. package.json에 기본으로 설정되어 있는 main 파일인 index.js를 생성한다. index.js는 기본 실행 파일이 된다.
4. config.json 파일을 만들고 봇의 토큰을 넣는다. 이 파일은 노출되면 안 되기 때문에 .gitignore에 명시해두어야 한다.
5. discord.js 패키지를 설치한다. 명령어는 `npm install discord.js` 다. 패키지가 설치되면 package.json의 dependencies 안에 discord.js가 추가된다.

### 2. 코드 모음

```json
{
    "token": "(여기에 복사해둔 토큰 넣기)"
}
```

<details>
<summary>일정 관리 코드</summary>
<div markdown="1">

```js
const { Client, Events, GatewayIntentBits } = require('discord.js');
const { token } = require('./config.json');

const client = new Client({ intents: [
    GatewayIntentBits.Guilds,        // 봇이 서버에 연결할 수 있게 함
    GatewayIntentBits.GuildMessages, // 봇이 채팅방의 메세지를 받을 수 있게 함
    GatewayIntentBits.MessageContent,// 봇이 메세지의 내용까지 읽을 수 있게 함
] });

// 봇이 켜졌을 때 로그 출력
client.once(Events.ClientReady, readyClient => {
    console.log(`Ready! Logged in as ${readyClient.user.tag}`);
});

// 메모리 기반 일정 저장소 - 일정 목록 저장
let scheduleList = [];
// 사용자별 등록 진행 중인 일정 저장
const pendingSchedules = new Map();
// 일정 수정 중인 사용자 관리 (날짜 포함 여부 처리)
const pendingEdits = new Map();

client.on('messageCreate', (message) => {
    const msg = message.content.trim();
    const userId = message.author.id;

    // !명령
    if (msg === '!명령') {
        return message.reply(
            '📋 사용 가능한 명령어:\n' +
            '`!일정` - 등록된 일정 목록 보기\n' +
            '`!일정등록 (일정명)` - 일정 등록\n' +
            '`!일정삭제 (일정명)` - 일정 삭제\n' +
            '`!일정수정 (기존이름) (새이름)` - 일정 이름 수정'
        );
    }

    // 날짜 입력 처리 - 일정 등록 또는 수정
    if (pendingSchedules.has(userId)) {
        const pending = pendingSchedules.get(userId);
        const dateRegex = /^\d{4}\.\d{2}\.\d{2}$/;

        if (!dateRegex.test(msg)) {
            return message.reply("❌ 날짜 형식이 잘못되었습니다. `YYYY.MM.DD` 형식으로 입력해주세요.");
        }

        const parsedDate = new Date(msg.replace(/\./g, '-'));
        if (isNaN(parsedDate.getTime())) {
            return message.reply("❌ 유효한 날짜가 아닙니다. 다시 입력해주세요.");
        }

        if (typeof pending.index === 'number') {
            // 일정 수정 중 날짜 변경
            scheduleList[pending.index].endDate = parsedDate;
            pendingSchedules.delete(userId);
            return message.reply(`📅 종료일이 \`${msg}\`로 수정되었습니다.`);
        }

        // 일정 등록
        if (scheduleList.find(s => s.name === pending.name)) {
            pendingSchedules.delete(userId);
            return message.reply(`⚠️ \`${pending.name}\` 일정은 이미 등록되어 있습니다.`);
        }

        scheduleList.push({
            name: pending.name,
            endDate: parsedDate
        });

        pendingSchedules.delete(userId);
        return message.reply(`✅ \`${pending.name}\` 일정이 \`${msg}\` 종료일로 등록되었습니다.`);
    }

    // 날짜 수정 여부 확인 처리
    if (pendingEdits.has(userId)) {
        const reply = msg.toLowerCase();
        const { index } = pendingEdits.get(userId);

        if (reply === '네') {
            pendingSchedules.set(userId, { name: scheduleList[index].name, index });
            pendingEdits.delete(userId);
            return message.reply(`📅 \`${scheduleList[index].name}\` 일정의 새로운 종료일을 입력해주세요. (YYYY.MM.DD)`);
        } else if (reply === '아니요') {
            pendingEdits.delete(userId);
            return message.reply("✅ 일정 이름만 수정되었습니다.");
        } else {
            return message.reply("❓ 종료일도 수정하시겠습니까? `네` 또는 `아니요`로 답해주세요.");
        }
    }

    // !일정
    if (msg === '!일정') {
        if (scheduleList.length === 0) {
            return message.reply('📭 등록된 일정이 없습니다. `!일정등록 일정명`으로 등록해보세요!');
        }

        const sorted = scheduleList.sort((a, b) => a.endDate - b.endDate);
        const formatted = sorted.map((item, i) => {
            const y = item.endDate.getFullYear();
            const m = String(item.endDate.getMonth() + 1).padStart(2, '0');
            const d = String(item.endDate.getDate()).padStart(2, '0');
            return `**${i + 1}.** 📌 ${item.name} (📅 ${y}.${m}.${d})`;
        }).join('\n');

        return message.reply(`📅 **종료일 기준 일정 목록:**\n\n${formatted}`);
    }

    // !일정등록
    if (msg.startsWith('!일정등록 ')) {
        const scheduleName = msg.replace('!일정등록 ', '').trim();
        if (!scheduleName) return message.reply('❌ 일정명을 입력해주세요.');

        if (pendingSchedules.has(userId)) {
            return message.reply("⚠️ 이미 일정 등록을 진행 중입니다. 종료일을 먼저 입력해주세요.");
        }

        pendingSchedules.set(userId, { name: scheduleName });
        return message.reply(`🗓️ \`${scheduleName}\` 일정의 종료일을 입력해주세요. (YYYY.MM.DD)`);
    }

    // !일정삭제
    if (msg.startsWith('!일정삭제 ')) {
        const scheduleName = msg.replace('!일정삭제 ', '').trim();
        const index = scheduleList.findIndex(s => s.name === scheduleName);

        if (index === -1) {
            return message.reply(`⚠️ \`${scheduleName}\` 일정은 존재하지 않습니다.`);
        }

        scheduleList.splice(index, 1);
        return message.reply(`🗑️ \`${scheduleName}\` 일정이 삭제되었습니다.`);
    }

    // !일정수정
    if (msg.startsWith('!일정수정 ')) {
        const args = msg.replace('!일정수정 ', '').trim().split(' ');
        if (args.length < 2) {
            return message.reply('❌ 사용법: `!일정수정 (기존이름) (새이름)`');
        }

        const [oldName, ...newNameParts] = args;
        const newName = newNameParts.join(' ').trim();
        const index = scheduleList.findIndex(s => s.name === oldName);

        if (index === -1) {
            return message.reply(`⚠️ \`${oldName}\` 일정은 존재하지 않습니다.`);
        }

        if (scheduleList.some(s => s.name === newName)) {
            return message.reply(`⚠️ \`${newName}\` 일정은 이미 존재합니다.`);
        }

        scheduleList[index].name = newName;
        pendingEdits.set(userId, { index });

        return message.reply(`✏️ 일정명이 \`${oldName}\` → \`${newName}\` 으로 변경되었습니다.\n📅 종료일도 수정하시겠습니까? (네/아니요)`);
    }
});

client.login(token);

```

</div>
<details>