const keyboard = document.querySelector('.keyboard');
// const empty = document.querySelector('.empty');
// empty.style.color = 'white';
let animating = [];
let animatingColor;
const keysDown = new Set();
const keyCodeToEle = new Map();
const allKeys = [ ...document.querySelectorAll('.key') ];
allKeys.forEach(ele => {
	const { code } = ele.dataset;
	if(code) {
		keyCodeToEle.set(code, ele);
	}
});
const macroKeys = [ document.querySelector('[data-code="Escape"]'), ...document.querySelectorAll('[data-macro]') ];
const allKeyBounds = allKeys.map(n => n.getBoundingClientRect());
const furthestKeys = {};
for(const macro of macroKeys) {
	const index = allKeys.indexOf(macro);
	const color = macro.dataset.macro;
	furthestKeys[color] = 0;
	const macroBounds = allKeyBounds[index];
	for(let i = 0; i < allKeys.length; i++) {
		const ele = allKeys[i];
		if(macro === ele) continue;
		const eleBounds = allKeyBounds[i];
		const d = dist(
			macroBounds.x + macroBounds.width * 0.5, macroBounds.y + macroBounds.height * 0.5,
			eleBounds.x + eleBounds.width * 0.5, eleBounds.y + eleBounds.height * 0.5
		);
		ele.macro = ele.macro || {};
		ele.macro[color] = d;
		if(d > furthestKeys[color]) {
			furthestKeys[color] = d;
		}
	}
}
macroKeys.forEach(ele => {
	const { macro } = ele.dataset;
	ele.addEventListener('click', () => {
		animating.push({ time: performance.now(), color: macro });
		if(animating.length === 1) {
			_draw();
		}
	});
});
window.addEventListener('keydown', e => {
	if(e.code.startsWith('F') && !isNaN(e.code.slice(1))) {
	   return;
	}
	e.preventDefault();
	// if(animating.length) return;
	// empty.textContent = e.code;
	// const ele = document.querySelector(`[data-code=${e.code}]`);
	const ele = keyCodeToEle.get(e.code);
	if(ele) {
		keysDown.add(allKeys.indexOf(ele));
		ele.dataset.selected = 'true';
	}
});
window.addEventListener('keyup', e => {
	e.preventDefault();
	// if(animating.length) return;
	// empty.textContent = e.code;
	// const ele = document.querySelector(`[data-code=${e.code}]`);
	const ele = keyCodeToEle.get(e.code);
	if(ele) {
		ele.dataset.selected = 'false';
		keysDown.delete(allKeys.indexOf(ele));
		if(macroKeys.includes(ele)) {
			const { macro } = ele.dataset;
			animating.push({ time: performance.now(), color: macro });
			if(animating.length === 1) {
				_draw();
			}
		}
	}
});
window.addEventListener('blur', e => {
	if(animating.length) animating.splice(0);
	for(const ele of document.querySelectorAll('[data-selected="true"], [data-color]')) {
		ele.dataset.selected = 'false';
		ele.dataset.color = '';
	}
});

function distSq(x1, y1, x2, y2) {
	const _x = x2 - x1, _y = y2 - y1;
	return _x * _x + _y * _y;
}
function dist(x1, y1, x2, y2) {
	const d = distSq(x1, y1, x2, y2);
	if(d === 0) return 0;
	return Math.sqrt(d);
}

function _draw(e) {
	draw(e);
	if(animating.length) {
		requestAnimationFrame(_draw);
	}
	else {
		for(const ele of document.querySelectorAll('[data-selected="true"], [data-color]')) {
			ele.dataset.selected = 'false';
			ele.dataset.color = '';
		}
	}
}

function draw(e) {
	if(!animating.length) return;
	const actions = Array(allKeys.length).fill(false);
	keysDown.forEach(i => actions[i] = true);
	const dilation = 100;
	for(let i = animating.length - 1; i >= 0; i--) {
		const a = animating[i];
		const time = e - a.time;
		const duration = furthestKeys[a.color] + dilation;
		if(time >= duration) {
			animating.splice(i, 1);
			return;
		}
		for(let keyIndex = 0; keyIndex < allKeys.length; keyIndex++) {
			const key = allKeys[keyIndex];
			const d = key.macro[a.color];
			const t = Math.abs(time - d);
			if(t < dilation && !actions[keyIndex]) {
				actions[keyIndex] = a.color;
			}
		}
	}
	for(let i = 0; i < actions.length; i++) {
		const key = allKeys[i];
		if(actions[i]) {
			key.dataset.selected = 'true';
			if(typeof actions[i] === 'string') {
				key.dataset.color = actions[i];
			}
		}
		else {
			key.dataset.color = '';
			key.dataset.selected = 'false';
		}
	}
}