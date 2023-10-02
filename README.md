why this script is not working the data on page is not updated correct the script
<script>
const backend_url = "http://127.0.0.1:8000";
function handleNoEvent() {
// no active events
console.log("No Active Events Found");
}
function handleEventRegSuccess(data) {
console.log("Success: " + data);
}
function handleEventRegError(error) {
if (error.status_code === 400) {
alert('Invalid Data: ' + error.message);
} else if (error.status_code === 406) {
alert('Duplicate Entry: ' + error.message);
} else if (error.status_code === 409) {
alert('Conflict: ' + error.message);
} else {
alert('Error: ' + error.message);
}
}
function setEventDetails(event_details) {
console.log("Setting Event Details");
document.getElementById('event_id').value = event_details.ref_id;
document.getElementById('event_domain').innerHTML = event_details.domain;
document.getElementById('event_title').innerHTML = event_details.title;
document.getElementById('event_desc').innerHTML = event_details.description;
document.getElementById('event_datetime').innerHTML = event_details.event_date_time;
document.getElementById('event_venue').innerHTML = event_details.venue;
document.getElementById('event_reg_limit').innerHTML = event_details.reg_count_limit;
document.getElementById('event_reg_count').innerHTML = event_details.reg_count;
document.getElementById('event_poster').src = backend_url + event_details.event_poster;
}
function getActiveEvent() {
fetch(backend_url + '/api/active_events')
.then(response => response.json())
.then(data => {
console.log(data);
if (data.length < 1) {
return null;
} else {
return data[0];
}
})
.catch(error => handleNoEvent());
}
// get event from url
function setEvent() {
console.log("Getting Event Details");
var event_id = new URLSearchParams(window.location.search).get('event');
var event;
if (event_id != null) {
fetch(backend_url + '/api/event/' + event_id)
.then(response => response.json())
.then(data => {
if (data.length < 1) {
event = getActiveEvent();
} else {
event = data[0];
}
}).then(() => {
if (event != null) {
setEventDetails(event);
} else {
handleNoEvent();
}
})
.catch(error => handleNoEvent());
}else{
event = getActiveEvent();
if (event != null) {
setEventDetails(event);
} else {
handleNoEvent();
}
}
}
// console.log(getParam('event'));
window.onload = setEvent();
document.getElementById('event_reg_form').addEventListener('submit', function (e) {
e.preventDefault();  // Stop the form from submitting normally
var url = backend_url + "/api/register_for_event";  // Get the URL from the form's action attribute
// Get form data
var data = new FormData(e.target);
// Use Fetch API to send a POST request
fetch(url, {
method: 'POST',
body: data
})
.then(response => {
if (response.ok) {
return response.json();
} else {
return response.json().then(error => {
throw error;
});
}
})
.then(data => {
let entryKey = data.entry_key;
console.log('Entry Key:', entryKey);
handleEventRegSuccess(data);
})
.catch((error) => {
console.error('Error:', error);
console.error('data:', data);
handleEventRegError(error);
});
});
</script>
may be there is synchronisation issue
the output on console is
Getting Event Details
active-event.html:299 No Active Events Found
active-event.html:335 [{…}]