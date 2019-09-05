# gk2019-cordova FIXES

## Enable multiple file picker
https://github.com/MatusFiala/cordova-android/commit/3e3a0b504283ebc491a2a3f497baadae3c5fa84c
1. edit this file:
	>`platforms\android\CordovaLib\src\org\apache\cordova\engine\SystemWebChromeClient.java`
1. find this method
	```java
	public boolean onShowFileChooser(WebView webView, final ValueCallback<Uri[]> filePathsCallback, final WebChromeClient.FileChooserParams fileChooserParams)
	```
1. replace it
	```java
	public boolean onShowFileChooser(WebView webView, final ValueCallback<Uri[]> filePathsCallback, final WebChromeClient.FileChooserParams fileChooserParams) {
		// Check if multiple-select is specified
        Boolean selectMultiple = false;
        if (fileChooserParams.getMode() == WebChromeClient.FileChooserParams.MODE_OPEN_MULTIPLE) {
            selectMultiple = true;
        }
        Intent intent = fileChooserParams.createIntent();
		intent.addCategory(Intent.CATEGORY_OPENABLE);
		intent.putExtra(Intent.EXTRA_ALLOW_MULTIPLE, selectMultiple);
        try {
            parentEngine.cordova.startActivityForResult(new CordovaPlugin() {
                @Override
                public void onActivityResult(int requestCode, int resultCode, Intent intent) {
                    Uri[] result = null;
                    if (resultCode ==  Activity.RESULT_OK && intent != null) {
                        if (intent.getClipData() != null) {
                            // handle multiple-selected files
                            final int numSelectedFiles = intent.getClipData().getItemCount();
                            result = new Uri[numSelectedFiles];
                            for (int i = 0; i < numSelectedFiles; i++) {
                                result[i] = intent.getClipData().getItemAt(i).getUri();
                                LOG.d(LOG_TAG, "Receive file chooser URL: " + result[i]);
                            }
                        }
                        else if (intent.getData() != null) {
                            // handle single-selected file
                            result = WebChromeClient.FileChooserParams.parseResult(resultCode, intent);
                            LOG.d(LOG_TAG, "Receive file chooser URL: " + result);
                        }
                    }
                    filePathsCallback.onReceiveValue(result);
                }
            }, intent, FILECHOOSER_RESULTCODE);
        } catch (ActivityNotFoundException e) {
            LOG.w("No activity found to handle file chooser intent.", e);
            filePathsCallback.onReceiveValue(null);
        }
        return true;
    }
	```
1. or simply replace with this file if there are no other differences
	>`FIXES/EnableMultipleFilePicker/SystemWebChromeClient.java`