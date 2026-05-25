---
layout: page
title: Contact Us
permalink: /contact/
nav_label: Contact
---


<form id="contact-form" method="POST" action="https://your-lambda-endpoint.amazonaws.com/contact" style="max-width: 500px; margin: 0 auto;">
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

<script>
document.getElementById('contact-form').addEventListener('submit', async function(e) {
	e.preventDefault();
	const form = e.target;
	const status = document.getElementById('form-status');
	status.textContent = '';
	const data = {
		firstName: form.firstName.value,
		lastName: form.lastName.value,
		email: form.email.value,
		phone: form.phone.value,
		message: form.message.value
	};
	try {
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
			status.style.color = 'red';
			status.textContent = 'Sorry, there was a problem sending your message.';
		}
	} catch (err) {
		status.style.color = 'red';
		status.textContent = 'Sorry, there was a problem sending your message.';
	}
});
</script>
