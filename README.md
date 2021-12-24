Работает 100% на версиях:<br>
1) Версия ядра 21.850.1000
2) Версия модуля sale 21.700.0

Если у вас не работает, обновитесь, возможно у вас старое ядро и модуль, и в них нет нужных зависимостей.
<br>

# Раскрываем все блоки на странице оформления заказа
###### В файле order_ajax.js данного репозитория все изменения уже внесены. Ниже просто описываем, что мы сделали.
## Работаем с файлом order_ajax.js

#### 1) Включаем редактирование блоков «Регион» и «Пользователь»: комментируем строки
```js
if (this.activeSectionId !== this.regionBlockNode.id)
    this.editFadeRegionContent(this.regionBlockNode.querySelector('.bx-soa-section-content'));

if (this.activeSectionId != this.propsBlockNode.id)
    this.editFadePropsContent(this.propsBlockNode.querySelector('.bx-soa-section-content'));
```
<br>

#### 2) Удаляем кнопки «Далее» и «Назад»: комментируем строки
```js
node.appendChild(
	BX.create('DIV', {
		props: {className: 'row bx-soa-more'},
		children: [
			BX.create('DIV', {
				props: {className: 'bx-soa-more-btn col-xs-12'},
				children: buttons
			})
		]
	})
);
```
#### 3) Раскрываем все блоки
###### Меняем эту строку
```js
var active = section.id == this.activeSectionId
```
###### На эту строку
```js
var active = true
```
#### 4) Удаляем обработчики при клике на заголовки: комментируем строки
```js
BX.unbindAll(titleNode);
if (this.result.SHOW_AUTH)
{
	BX.bind(titleNode, 'click', BX.delegate(function(){
		this.animateScrollTo(this.authBlockNode);
		this.addAnimationEffect(this.authBlockNode, 'bx-step-good');
		}, this));
}
else
{
	BX.bind(titleNode, 'click', BX.proxy(this.showByClick, this));
	editButton = titleNode.querySelector('.bx-soa-editstep');
	editButton && BX.bind(editButton, 'click', BX.proxy(this.showByClick, this));
}
```
#### 5) Удаляем ссылки «Изменить»: в конец функции editOrder добавляем код
```js
var editSteps = this.orderBlockNode.querySelectorAll('.bx-soa-editstep'), i;
for (i in editSteps)
{
	if (editSteps.hasOwnProperty(i))
	{
		BX.remove(editSteps[i]);
	}
}
```

#### P.S. Для понимания покажем весь код функции
```js
editOrder: function()
{
	if (!this.orderBlockNode || !this.result)
		return;

	if (this.result.DELIVERY.length > 0)
	{
		BX.addClass(this.deliveryBlockNode, 'bx-active');
		this.deliveryBlockNode.removeAttribute('style');
	}
	else
	{
		BX.removeClass(this.deliveryBlockNode, 'bx-active');
		this.deliveryBlockNode.style.display = 'none';
	}

	this.orderSaveBlockNode.style.display = this.result.SHOW_AUTH ? 'none' : '';
	this.mobileTotalBlockNode.style.display = this.result.SHOW_AUTH ? 'none' : '';

	this.checkPickUpShow();

	var sections = this.orderBlockNode.querySelectorAll('.bx-soa-section.bx-active'), i;
	for (i in sections)
	{
		if (sections.hasOwnProperty(i))
		{
			this.editSection(sections[i]);
		}
	}

	this.editTotalBlock();
	this.totalBlockFixFont();

	this.showErrors(this.result.ERROR, false);
	this.showWarnings();
			
	// добавили этот код ========
        var editSteps = this.orderBlockNode.querySelectorAll('.bx-soa-editstep'), i;
	for (i in editSteps)
	{
		if (editSteps.hasOwnProperty(i))
		{
			BX.remove(editSteps[i]);
		}
	}
	// =========
}
```