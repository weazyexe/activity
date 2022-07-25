> You can find this article also at [habr.ru](https://habr.com/ru/company/surfstudio/blog/678294/)

![Обложка](https://github.com/weazyexe/activity/blob/master/articles/accessibility_views/assets/cover.jpeg)

# Accessibility в Android для чайников: адаптируем приложение для незрячих

В один из зимних вечеров я сидел дома, читал замечательную книжку «Android-программирование для профессионалов» и наткнулся на главу про Accessibility.

Раньше я об этой теме не задумывался: клал `null` в поле `contentDescription` и жил себе спокойно. Но когда получил поверхностные знания, как слабовидящие люди пользуются смартфоном через утилиту TalkBack, у меня появилось желание погрузиться в тему доступности глубже.

Меня зовут Тимур Задворнов, я Android-разработчик в Surf. Расскажу и покажу, как работает Accessibility в Android. Посмотрим, легко ли адаптировать приложения для людей с ограниченными возможностями — или это слишком трудоёмкая задача, к которой даже не стоит подступаться.

![Content Description meme](https://github.com/weazyexe/activity/blob/master/articles/accessibility_views/assets/contentdescription_meme.png)

Погружаться в тему Accessibility я начал с гуглежа статистики. [По данным ВОЗ](https://www.who.int/news-room/fact-sheets/detail/disability-and-health), в мире более миллиарда человек обладает с той или иной формой инвалидности. «Немало», — подумал я и сразу задался другим вопросом: а почему, если в мире так много людей с инвалидностью, огромное количество популярных и не очень приложений не поддерживают приложения для слабовидящих, глухих, людей с заболеваниями опорно-двигательного аппарата? Решил, что надо исправлять это недоразумение и двигать Accessibility в массы. 

## Для начала разберемся, что вообще такое Accessibility

![Да кто такой этот ваш Accessibility](https://github.com/weazyexe/activity/blob/master/articles/accessibility_views/assets/accessibility_who.png)

Accessibility в переводе с английского — «доступность». В контексте программного обеспечения — разработка ПО таким образом, чтобы им могли пользоваться люди с ограниченными возможностями. У термина есть общепринятое сокращение — *a11y*: между *a* и *y* в слове *accessibility* 11 букв.

Чтобы разобраться, как работает Accessibility, предлагаю задаться вопросом: а как в целом люди пользуются смартфоном? Пользователь взаимодействует с устройством, передавая ему действия: нажатия, свайпы, потряхивания. Смартфон возвращает результат действия, отображая его на экране. 

Мы взаимодействуем со смартфоном как привыкли: нажимаем на экран и получаем результат действия. Люди с ограниченными возможностями пользуются смартфоном точно так же, просто они передают и получают информацию со смартфона другими путями: отправляют голосовые команды, команды с другого устройства ввода. Способов много — подход общий.

![Пользователь передает действия, получает информацию](https://github.com/weazyexe/activity/blob/master/articles/accessibility_views/assets/phone_usage_graph.png)
<p align="center">Получаем данные, отправляем действия, чтобы получить новые данные</p>

В Android существуют встроенные Accessibility Services: TalkBack, Switch Access, Voice Access и прочие. О них можно думать как о плагинах для Android, которые помогают людям с ограниченными возможностями пользоваться смартфоном.

> Под термином «Accessibility Services» будут подразумеваться перечисленные сервисы, а не Android-сервисы для работы в бэкграунде. Пишу об этом, потому что в Android есть одноименный сервис — AccessibilityService, который работает в бэкграунде с отправляемыми ему AccessibilityEvent.

## Какие Accessibility Services существуют

С каждой новой версией Android выкатывает всё больше новых сервисов для пользователей с ограниченными возможностями. Сейчас в Android есть:

* TalkBack — зачитывает информацию о UI-компонентах;
* Switch Access — управление устройством через специальный контроллер;
* Voice Access — голосовое управление смартфоном;
* Select to Speak — зачитывает текстовый контент в выделенной области;
* Extra Dim — настройки яркости дисплея;
* Magnification — экранная лупа;
* Live Captions — экранные субтитры;
* Live Transcribe — перевод речи в текст;
* Sound Amplifier — управление звуком;
* Lookout — распознавание объектов через камеру;
* Action Blocks — создание сценариев использования.

И это ещё, возможно, не всё! 

Остановимся подробнее на первых трёх сервисах: TalkBack, Switch Access, Voice Access. Их используют чаще всего: именно с ними придётся сталкиваться разработчику в повседневной работе. Остальные — вспомогательные. Более подробно про них можно посмотреть в [официальной документации](https://www.android.com/accessibility/).

### TalkBack

TalkBack — утилита, которая зачитывает информацию с экрана. Помогает слабовидящим людям пользоваться смартфоном. 

TalkBack меняет логику управления смартфоном: пользователь выделяет нужный компонент, и система зачитывает содержимое. Компоненты экрана можно выделять либо тапом, свайпами влево и вправо или зажать палец на экране и двигать его — тогда TalkBack будет зачитывать каждый элемент, на который наведён жест. Свайп вправо — выделится следующий элемент на экране, свайп влево — предыдущий. 

Все привычные обычному пользователю жесты — назад, свернуть, меню многозадачности — работают, но их нужно воспроизводить двумя пальцами. Все действия и жесты TalkBack описаны в [документации](https://support.google.com/accessibility/android/answer/6151827).

На каждом смартфоне с Android на борту можно включить TalkBack и пройти туториал по использованию: всё это делается в настройках, пункт Accessibility. 

https://user-images.githubusercontent.com/35996778/180798035-b48b74fc-10cc-4fe1-902a-4d1c2ed4f230.mp4

> В настройках TalkBack есть меню с настройками для разработчиков. Можно включить субтитры или добавить шорткат быстрого включения и выключения TalkBack — зажать кнопки громкости на несколько секунд. Очень помогает во время разработки.

### Switch Access

Switch Access — сервис для управления смартфоном с помощью специального устройства, переключателя. 

![Так может выглядеть переключатель](https://github.com/weazyexe/activity/blob/master/articles/accessibility_views/assets/switch.png)
<p align="center">Так может выглядеть переключатель</p>

Принцип взаимодействия со смартфоном схож с принципом работы через TalkBack. На экране выделен компонент, между компонентами можно переключаться. Обычно на переключателе есть несколько кнопок. В качестве переключателя можно использовать специальное устройство или настроить  стороннюю клавиатуру, а также кнопки громкости на смартфоне для удобного дебаггинга.

### Voice Access

Voice Access — сервис для голосового управления смартфоном. Пользователь говорит, какое действие нужно сделать на экране: нажать кнопку «Лайк», пролистать вниз, ввести текст в поле «Сообщение». Действие выполняется. На видео — Voice Access в действии.

https://user-images.githubusercontent.com/35996778/180798614-15a3d16b-5d6b-4036-9377-7201f80b1ba8.mp4

На самом деле Voice Access далеко не идеальная вещь: иногда тупит и некорректно распознаёт слова — особенно названия приложений.

## Что происходит со стороны разработчиков

Давайте посмотрим, какие инструменты есть для разработчиков, чтобы UX приложений не страдал. Хорошие новости: все компоненты из стандартной библиотеки Android по умолчанию поддерживают Accessibility. Не придётся с нуля разбираться и делать доступными базовые компоненты: TextView, EditText, Switch и другие.

### Приложение Accessibility Scanner для помощи разработчикам

Google выпустил для разработчиков приложение *Accessibility Scanner*: оно помогает найти некомфортные для людей с ограниченными возможностями места в приложении. 

*Accessibility Scanner* работает просто:

* Открываем, даём все разрешения, оно отображается над всеми приложениями как плавающая кнопка.
* Заходим в приложение, которое нужно проверить, переходим на нужный экран, нажимаем на кнопку Snapshot.
* Ждём, пока Accessibility Scanner проанализирует экран, и смотрим на результат.

![Accessibility Scanner](https://github.com/weazyexe/activity/blob/master/articles/accessibility_views/assets/scanner_results.png)
<p align="center">Результаты работы Accessibility Scanner на экране приложения Twitter. Оранжевым подсвечиваются проблемные места. Есть подсказки, как исправить ошибки, с пояснениями, почему нужно сделать так</p>

Accessibility Scanner — отличная штука, но у него есть существенный недостаток. Сканер находит только очевидные проблемы: размер текста, размер кликабельной области компонента, отсутствие лейбла у изображения и так далее. В более сложных случаях он бесполезен. Например, не поможет с распознаванием кастомных вью и правильной интерпретацией для слабовидящих: что зачитывать с этого компонента, какие действия с ним можно совершить.

Давайте разберемся, как сделать приложение более доступным с помощью нескольких простых шагов: они закроют процентов 90 всевозможных кейсов.

### Шрифты

Правило простое: всегда указывайте размер шрифтов в sp! Вы меня спросите: «Зачем для шрифтов придумали какую-то отдельную единицу? Если я укажу размер в dp, никто не умрёт». 

![Well yes but actually no](https://github.com/weazyexe/activity/blob/master/articles/accessibility_views/assets/well_yes.png)
<p align="center">Если бы всё было так просто, мои вы хорошие</p>

Если пользователь увеличит шрифты в системе, нужно, чтобы они увеличились и в приложении. Для этого и пригодится sp-юнит: если указать размер текста 16sp, а пользователь в системе поставил коэффициент увеличения текста = 1,25, то размер шрифта станет 20dp. На иллюстрациях видно, как изменяется размер шрифта, указанный в sp.

![Сравнение размеров шрифта в sp и dp](https://github.com/weazyexe/activity/blob/master/articles/accessibility_views/assets/fonts.png)

### Заголовки

TalkBack предоставляет удобную навигацию между заголовками. Если на экране много текста с заголовками или, например, на экране есть список с подсписками, которые также помечены заголовками, в эти TextView дополнительно следует положить `true` в поле `accessibilityHeading`. Тогда текст будет считываться как заголовок. 

Этот параметр появился в Android 9. Если нужно использовать его на более старых версиях, эту проблему решает compat-версия этого параметра — `ViewCompat.setAccessibilityHeading(view, boolean)`.

### Контраст

Люди с нарушениями зрения могут воспринимать цвета по-другому: стоит следить, все ли элементы на экране чётко различимы между собой, есть ли цветовой контраст. [По гайдлайнам Android](https://support.google.com/accessibility/android/answer/7158390?hl=en), хорошая практика — устанавливать цветовой контраст на наложенных друг на друга компонентах с соотношением более чем 3:1 для больших текстов и 4,5:1 для остального контента.

Что это за цифры? Это соотношение между цветами, которое показывает, насколько сильно контрастируют два цвета между собой. Чтобы было понятно, разберем на примере: соотношение белого цвета (#FFFFFF) к чёрному (#000000) равняется 21:1 — это максимальное возможное соотношение. Два одинаковых цвета имеют отношение 1:1. Проверить контраст между цветами можно на [сайте WebAIM](https://webaim.org/resources/contrastchecker/).

![Сравнение контрастов](https://github.com/weazyexe/activity/blob/master/articles/accessibility_views/assets/well_yes.png)
<p align="center">Пример плохого и хорошего контрастов</p>

### КАПС – НЕ НАШ БРО

На некоторых девайсах TalkBack читает текст, написанный заглавными буквами, как аббревиатуру – по букве. Сами понимаете, воспринимать это на слух очень трудно.

Заглавными буквами следует писать только аббревиатуры. Основной текст — строчными буквами или sentence case. Если всё-таки нужно оставить текст заглавными буквами, используйте параметр `android:textAllCaps=”true”` — и тогда всё будет ок.

### Размеры элементов

Любые *кликабельные* элементы на экране должны иметь размер как минимум 48x48dp. Такой размер [рекомендован Google](https://support.google.com/accessibility/android/answer/7101858?hl=en): область нажатия пальцем на экран равняется приблизительно этому значению. В вебе, например, [рекомендованный размер 44x44px](https://www.w3.org/WAI/WCAG21/Understanding/target-size.html).

### Content Description

Не будем лукавить: все мы когда-то устанавливали поле `contentDescription` в `null`, потому что «да зачем оно мне надо». Но если вы хотите, чтобы приложение было доступным, придётся разбираться, что это за поле за такое.

Поле `contentDescription` используется в основном для визуальных компонентов без текста — ImageButton, ImageView и так далее. Это нужно, чтобы TalkBack при наведении на этот элемент не зачитал *«Unlabeled, Button, double-tap to activate»*, а дал конкретную информацию: что за элемент, зачем он нужен на экране и что произойдет, если нажать на него. Эта строка обязательно должна быть локализована: если пользователь из Франции запустит TalkBack, а ему на чистом японском зачитают «дескрипшен», вряд ли кому-то от этого станет легче. И вообще: любые строки, связанные с a11y, будь то `contentDescription` или дополнительные действия TalkBack, о которых мы поговорим далее, __должны быть локализованы__.

> В этой статье я буду использовать английскую версию TalkBack и локализованные строки так же на английском языке. Не беспокойтесь: на русском TalkBack тоже работает.

Если ImageView имеет на экране чисто декоративную роль, `contentDescription` можно не указывать. Тогда важно указать другой параметр — `importantForAccessibility`. Он отвечает за необходимость прочтения TalkBack содержимого этого компонента. У этого параметра есть значения:

* `yes` — компонент обязателен для прочтения TalkBack.
* `no` — компонент пропускается.
* `noHideDescendants` — компонент и его дочерние компоненты пропускаются.
* `auto` — система сама определяет, обязателен ли компонент для TalkBack. Это значение по умолчанию.

### Live Regions

Иногда нужно при действии с одним компонентом на экране обновить значение компонента, не находящегося в фокусе. Для этого у View в Android есть поле `accessibilityLiveRegion`, которое позволяет зачитать значение из вью, если оно было изменено. У `accessibilityLiveRegion` есть три возможных значения:

* `none` — ничего не обновлять, значение по умолчанию.
* `polite` — если значение обновилось, но TalkBack ещё воспроизводит старое значение, новое значение зачитается после того, как TalkBack закончит говорить.
* `assertive` — противоположное значению `polite`. При обновлении значения TalkBack перестанет зачитывать старое значение и сразу начнёт зачитывать новое.

Проще понять на примере: у нас есть кнопка Increment, которая увеличивает значение на 1 и отображает новое значение в TextView. Изменяем значение `accessibilityLiveRegion` у TextView на `polite` и вот какой будет результат.

https://user-images.githubusercontent.com/35996778/180798854-2a5ed1e0-0225-4d4b-be63-a219a4cd803c.mp4


### Порядок навигации между View

В идеале порядок должен идти слева направо, сверху вниз, но иногда этот порядок приходится менять для более удобной навигации между компонентами. На картинке ниже — как раз такой пример. Он надуманный, это можно сделать гораздо лучше: объединить заголовок и значение или использовать параметр `labelFor`.

![Неправильный порядок навигации](https://github.com/weazyexe/activity/blob/master/articles/accessibility_views/assets/traversal_wrong.png)

На этом примере хотелось бы, чтобы после чтения заголовка читалось значение, для которого предназначен этот заголовок. Чтобы изменять порядок навигации, у всех View есть поле `accessibilityTraversalBefore`. В него нужно передать идентификатор компонента, на который мы хотим переключаться после фокуса текущей выделенной вью.

Также у View есть противоположный параметр — `accessibilityTraversalAfter`. В нём лежит идентификатор компонента, после которого текущий компонент будет выделен TalkBack. Иногда случается, что нужно задать параметр динамически через код: для этого у объекта класса `AccessibilityNodeInfo` вызвать метод `setTraversalBefore` или `setTraversalAfter`. Подробнее про `AccessibilityNodeInfo` и `AccessibilityDelegate` расписано далее.

Давайте укажем к каждому TextView с заголовком параметр `android:accessibilityTraversalBefore="@id/value_textview"`, где `@id/value_textview` — идентификатор соответствующего заголовка TextView со значением. Получим следующий порядок навигации:

![Верный порядок навигации](https://github.com/weazyexe/activity/blob/master/articles/accessibility_views/assets/traversal_right.png)

### Кастомные вью

Обычно кастомные вью состоят из нескольких более простых компонентов: текстов, кнопок, свитчеров. Хорошая практика — делать так, чтобы компонент выделялся TalkBack как единый, а не выделял отдельно компоненты-составные, и предлагал какие-то действия что можно сделать с этим сложным компонентом.

Понятнее будет, если разобраться на примерах. Предположим, есть компонент деталей криптокошелька. Возможные составляющие: название, баланс, адрес, кнопки пополнить и перевести.

![Кастомная вью, которую мы будем делать доступной](https://github.com/weazyexe/activity/blob/master/articles/accessibility_views/assets/wallet_card.png)
<p align="center">Кастомная вью, которую мы будем делать доступной</p>

```XML
<?xml version="1.0" encoding="utf-8"?>
<com.google.android.material.card.MaterialCardView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    style="?attr/materialCardViewElevatedStyle"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="16dp"
    android:clickable="true"
    android:focusable="true"
    app:cardCornerRadius="16dp">

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="16dp">

        <TextView
            android:id="@+id/title_tv"
            style="@style/TextAppearance.Material3.BodyLarge"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            tools:text="Main wallet" />

        <TextView
            android:id="@+id/balance_tv"
            style="@style/TextAppearance.Material3.HeadlineLarge"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textStyle="bold"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/title_tv"
            tools:text="ETH 51.7075000194" />

        <TextView
            android:id="@+id/address_tv"
            style="@style/TextAppearance.Material3.BodySmall"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:ellipsize="middle"
            android:maxEms="11"
            android:maxLines="1"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/balance_tv"
            tools:text="0xAb5801a7D398351b8bE11C439e05C5B3259aeC9B" />

        <ImageButton
            android:id="@+id/qr_code_button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:background="?attr/selectableItemBackgroundBorderless"
            android:padding="12dp"
            android:src="@drawable/ic_qr_code"
            app:layout_constraintBottom_toBottomOf="@id/address_tv"
            app:layout_constraintStart_toEndOf="@id/address_tv"
            app:layout_constraintTop_toTopOf="@id/address_tv" />

        <Button
            android:id="@+id/deposit_button"
            style="@style/Widget.Material3.Button.TonalButton.Icon"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginStart="8dp"
            android:text="@string/wallet_card_deposit"
            app:icon="@drawable/ic_arrow_downward"
            app:layout_constraintBaseline_toBaselineOf="@id/transfer_button"
            app:layout_constraintStart_toEndOf="@id/transfer_button" />

        <Button
            android:id="@+id/transfer_button"
            style="@style/Widget.Material3.Button.TonalButton.Icon"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="48dp"
            android:text="@string/wallet_card_transfer"
            app:icon="@drawable/ic_arrow_upward"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/balance_tv" />

    </androidx.constraintlayout.widget.ConstraintLayout>

</com.google.android.material.card.MaterialCardView>
```

По умолчанию, если ничего не делать, TalkBack просто зачитает текстовое содержимое компонента: название кошелька, баланс и адрес.

![Кастомная вью, которую мы будем делать доступной](https://github.com/weazyexe/activity/blob/master/articles/accessibility_views/assets/wallet_card_talkback.png)
<p align="center">Что зачитывает TalkBack</p>

Как можно улучшить UX для незрячих? Сделаем компонент более доступным за несколько простых шагов.

1\. Все дочерние элементы пометить `importantForAccessibility = no`, чтобы TalkBack не читал ничего, кроме кнопок «пополнить» и «перевести». С ними разберёмся позже.

```xml
<TextView
    android:id="@+id/title_tv"
    style="@style/TextAppearance.Material3.BodyLarge"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:importantForAccessibility="no"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent"
    tools:text="Main wallet" />
```

2\. Даём корневому элементу понятный `contentDescription`. Нужно определить, какая информация полезна для прочтения TalkBack. Пользователю будет полезно знать название кошелька и баланс. Обновлять `contentDescription` будем в функции `setData`, которая вызывается при инициализации компонента.

```kotlin
fun setData(
    title: String,
    balance: String,
    address: String
) = with(binding) {
	titleTv.text = title
    balanceTv.text = balance
    addressTv.text = address
	// Строка со значением "Wallet card: %s, balance is %s"
	root.contentDescription = context.getString(
        R.string.wallet_card_accessibility_description,
        title,
        balance
    )
}
```

3\. Обрабатываем взаимодействие с компонентом. Какой смысл этого компонента? Что он делает? Выделим список возможных действий:
* По нажатию на карточку переходим на экран деталей по кошельку.
* По нажатию на кнопку «Перевести» или «Пополнить» переходим на экран переводов. *С кнопками делать ничего не нужно, мы их не помечали как игнорируемые и они по умолчанию поддерживают accessibility.*
* Возможность скопировать адрес кошелька.
* Возможность отобразить адрес в формате QR-кода.

Для перехода к деталям кошелька почти ничего не требуется, если уже назначен слушатель на событие нажатия на карточку. Единственное, что стоит переопределить, — название действия. По умолчанию при нажатии на кликабельный элемент TalkBack будет зачитывать *«Double tap to activate»* Это понятно, если элемент — кнопка или свитчер. Но если компонент сложный, *«activate»* не передаёт нужного контекста. Вот бы было можно заменить описание действия, дав больше контекста. И — сюрприз — так можно!

Каждая вью содержит сущность `AccessibilityDelegate`. Согласно [документации](https://developer.android.com/reference/android/view/View.AccessibilityDelegate), делегат позволяет делать компоненты доступными не с помощью наследования, а с помощью композиции. Он может перехватывать события с UI, создавать кастомные экшны, редактировать существующие — и ещё много вещей, которые прокачают доступность компонентов.

Давайте создадим делегата для нашей карточки. Будем использовать класс `ViewCompat` для обратной совместимости. Всё это опишем в методе `setupAccessibility`, который также вызываем при инициализации компонента.

```kotlin
ViewCompat.setAccessibilityDelegate(
    binding.root,
    object : AccessibilityDelegateCompat() {
        override fun onInitializeAccessibilityNodeInfo(
            host: View,
            info: AccessibilityNodeInfoCompat
        ) {
            super.onInitializeAccessibilityNodeInfo(host, info)
            val customAction = AccessibilityNodeInfoCompat.AccessibilityActionCompat(
                AccessibilityNodeInfoCompat.ACTION_CLICK,
                // Строка со значением "View wallet details"
				context.getString(R.string.wallet_card_accessibility_card_click_action)
            )
            info.addAction(customAction)
        }
    }
)
```

Вы великолепны. Теперь TalkBack при наведении на карточку зачитает действие как *«Double tap to view wallet details»*.

Разберёмся с функциональностью, связанной с адресом. Для компонента помимо основных действий — нажатия,  долгого нажатия и так далее — можно добавлять кастомные действия и вызывать их через меню TalkBack. Чтобы открыть меню, нужно при включенном TalkBack сделать L-образный свайп сверху вниз или свайп тремя пальцами вниз — если версия TalkBack выше 9.1. Для добавления кастомного действия используются:

* Функция `ViewCompat.addAccessibilityAction`, куда передаём корневую вью и название действия.
* Класс `AccessibilityViewCommand`, в которое кладём код с действием, — просто лямбда.

```kotlin
private fun setupAccessibility() {
    
    . . .

    ViewCompat.addAccessibilityAction(
        binding.root,
        // Строка "Show address QR code"
        context.getString(R.string.wallet_card_accessibility_show_qr_code_action)
    ) { _, _ ->
        // Открываем диалог с QR здесь
        Toast.makeText(context, "QR code has shown", Toast.LENGTH_SHORT).show()
        true
    }
}
```

По такой же логике добавляем второе действие — копирование адреса кошелька.

```kotlin
private fun setupAccessibility() {
    
    . . .

    ViewCompat.addAccessibilityAction(
        binding.root,
        // Строка "Copy the wallet address"
        context.getString(R.string.wallet_card_accessibility_copy_the_address_action)
    ) { _, _ ->
        // Кладём строку с адресом в буфер обмена
        Toast.makeText(context, "Address has copied", Toast.LENGTH_SHORT).show()
        true
    }
}
```

Кастомная вью теперь гораздо доступнее, чем была до этого. Вот так это будет выглядеть со стороны пользователя.

https://user-images.githubusercontent.com/35996778/180798985-d9453539-33e1-4528-b45b-50dc8e4d1512.mp4
