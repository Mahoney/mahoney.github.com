# Link to Splunk from a Grafana Dashboard

It is possible to link from a Grafana Dashboard to a Splunk search for exactly the period of
time you are looking at in the Grafana dashboard.

Steps:
1. Go to `Dashbaord settings -> Links`
2. Click `New`
3. Select `Type: link`, `Title`, `Tooltip` and `Icon` as you prefer and leave all the `Include` options toggled off
4. Set the URL to:
  `https://<your_splunk_host>/en-GB/app/search/search?q=search%20index%3D<your_index>%20sourcetype%3D%22<your_sourcetype>%22%20%5B%20%7C%20makeresults%20%7C%20eval%20earliest%3D(floor($__from%2F1000))%20%7C%20eval%20latest%3D(floor($__to%2F1000))%20%7C%20return%20earliest%20latest%20%5D`
5. Click `Add`
6. Save the dashboard

Obviously you can alter any other search parameters you want. The important part of the URL for this purpose is:

`%20%5B%20%7C%20makeresults%20%7C%20eval%20earliest%3D(floor($__from%2F1000))%20%7C%20eval%20latest%3D(floor($__to%2F1000))%20%7C%20return%20earliest%20latest%20%5D`

which decodes to:

` [ | makeresults | eval earliest=(floor($__from/1000)) | eval latest=(floor($__to/1000)) | return earliest latest ]`

Grafana will replace `$__from` and `$__to` with the current view's epoch milliseconds, and this part of the query converts
them to epoch seconds, which is what splunk accepts.
