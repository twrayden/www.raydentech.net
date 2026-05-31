---
layout: page
title: Contact Us
permalink: /contact/
nav_label: Contact
---


<form id="contact-form" method="POST" action="https://9lnm1zhzbb.execute-api.ap-southeast-2.amazonaws.com/submit-contact-us-form">
	<label for="firstName">First Name*</label>
	<input type="text" id="firstName" name="firstName" required>

	<label for="lastName">Last Name*</label>
	<input type="text" id="lastName" name="lastName" required>

	<label for="email">Email*</label>
	<input type="email" id="email" name="email" required>

	<label for="phone">Phone</label>
	<input type="tel" id="phone" name="phone">

	<label for="message">Message*</label>
	<textarea id="message" name="message" rows="5" required></textarea>

	<button type="submit">Send Message</button>
	<div id="form-status" style="margin-top: 1em;"></div>
</form>

<script src="https://www.google.com/recaptcha/enterprise.js?render=6LeB4AUtAAAAAGB24jh34gjVN6ADPWHBtQy_E32S"></script>
<script>
const recaptchaSiteKey = '6LeB4AUtAAAAAGB24jh34gjVN6ADPWHBtQy_E32S';
const recaptchaAction = 'submit';

function getRecaptchaToken() {
	return new Promise((resolve, reject) => {
		if (!window.grecaptcha || !window.grecaptcha.enterprise) {
			reject(new Error('reCAPTCHA failed to load'));
			return;
		}
		grecaptcha.enterprise.ready(() => {
			grecaptcha.enterprise.execute(recaptchaSiteKey, { action: recaptchaAction })
				.then(resolve)
				.catch(reject);
		});
	});
}

document.getElementById('contact-form').addEventListener('submit', async function(e) {
	e.preventDefault();
	const form = e.target;
	const status = document.getElementById('form-status');
	const submitButton = form.querySelector('button[type="submit"]');
	status.textContent = '';
	status.style.color = '#222';
	status.textContent = 'Sending...';
	submitButton.disabled = true;
	try {
		const token = await getRecaptchaToken();
		const data = {
			firstname: form.firstName.value,
			lastname: form.lastName.value,
			email: form.email.value,
			mobile: form.phone.value,
			message: form.message.value,
			'g-recaptcha-response': token,
			pageUri: window.location.href,
			pageName: document.title
		};
		const res = await fetch(form.action, {
			method: 'POST',
			headers: { 'Content-Type': 'application/json' },
			body: JSON.stringify(data)
		});
		if (res.ok) {
			status.style.color = 'green';
			status.textContent = 'Thank you! Your message has been sent.';
			form.reset();
		} else {
			let errorText = 'Sorry, there was a problem sending your message.';
			try {
				const errorBody = await res.json();
				errorText = errorBody.error || errorBody.details || errorText;
			} catch (parseError) {
				const rawText = await res.text();
				if (rawText) {
					errorText = rawText;
				}
			}
			status.style.color = 'red';
			status.textContent = errorText;
		}
	} catch (err) {
		status.style.color = 'red';
		status.textContent = err.message || 'Sorry, there was a problem sending your message.';
	} finally {
		submitButton.disabled = false;
	}
});
</script>
