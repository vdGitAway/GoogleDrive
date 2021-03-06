<h2>About</h2>
Use for common operations with Google Drive. All methods are asynchronous. All implementations use Google.Apis.Drive.v3.

<h3>Methods for general operations.</h3>
<h4>GoogleDrive Class</h4>
<h5>All methods are async.</h5>
<table>
<tr><td>Reauthenticate()</td><td>Initialize or reinitialize oauth2 authentication with Google</td></tr>
<tr><td>DeleteFile(String id) returns Boolean</td><td>SELF-EXPLANATORY</td></tr>
<tr><td>UpdateFile(String filePath, String fileId) returns String"</td><td>SELF-EXPLANATORY</td></tr>
<tr><td>CreateFile(String filePath, String folderId, Boolean isPublic) returns String"</td><td>SELF-EXPLANATORY</td></tr>
<tr><td>ShareLinkToggle(String fileId, Boolean isOn) returns GoogleDriveFileData"</td><td>Turn share link on and off</td></tr>
<tr><td>CreateFolder(String name, String folderId, Boolean isPublic) returns String"</td><td>"Note, isPublic=true will turn share link on after folder creation</td></tr>
<tr><td>GetFileById(String id) returns GoogleDriveFileData</td><td>SELF-EXPLANATORY</td></tr>
<tr><td>DownloadFile(String fileID, String fullPathAndFileName)"</td><td>SELF-EXPLANATORY</td></tr>
<tr><td>DeleteEmptyFolder(String folderID) returns Boolean</td><td>Deletes any subfolder with empty content within the current folder</td></tr>
<tr><td>GetFilesInFolder(String folderID) returns List of GoogleDriveFileData</td><td>SELF-EXPLANATORY</td></tr>
<tr><td>GetFiles(String search, String field) returns List of GoogleDriveFileData"</td><td>SELF-EXPLANATORY</td></tr>
<tr><td>Action<float> ProgressCallbackCreate</td><td>Assign an action delegate to this property to get upload progress</td></tr>
<tr><td>Action<float> ProgressCallbackUpdate</td><td>Assign an action delegate to this property to get update progress</td></tr>
</table>

<h3>Data returns.</h3>
<h4>GoogleDriveFileData Class</h4>
<table>
<tr><td>String Name</td><td>Name of file or folder</td></tr>
<tr><td>String Id</td><td>SELF-EXPLANATORY</td></tr>
<tr><td>List of String Parents</td><td>One level up folder ID</td></tr>
<tr><td>Boolean IsShared</td><td>Share link active or not</td></tr>
<tr><td>String Link</td><td>URL of share link</td></tr>
<tr><td>List of String Permissions</td><td>SELF-EXPLANATORY</td></tr>
<tr><td></td></tr>
</table>


<h3>Sample unit tests</h3>

```csharp
//all method also test for Delete(), and ShareLink also test for GetFile()
    [TestClass]
    public class GoogleDriveUT
    {
        private readonly List<string> _toDeleteLater = new List<string>();
        private readonly string clientIdFile = @"..\Assets\client_id.json";
        private readonly string applicaitonName = "Unit test only";
        private readonly string uploadfile = @"..\Assets\s.mp4";

        [TestMethod]
        public async Task CreateFolder()
        {
            var instance = await
                GoogleDrive.GoogleDrive.Factory(applicaitonName, clientIdFile);
            var id = await instance.CreateFolder(Guid.NewGuid().ToString());
            var find = await instance.GetFileById(id);
            Assert.AreEqual(id, find.Id);
            Assert.AreEqual(true, await instance.DeleteFile(id));
        }

        [TestMethod]
        public async Task UploadFile()
        {
            var instance = await
                GoogleDrive.GoogleDrive.Factory(applicaitonName, clientIdFile);
            var id = await instance.CreateFile(uploadfile);
            var find = await instance.GetFileById(id);
            Assert.AreEqual(id, find.Id);
            Assert.AreEqual(true, await instance.DeleteFile(id));
        }

        [TestMethod]
        public async Task ShareLink()
        {
            var instance = await
                GoogleDrive.GoogleDrive.Factory(applicaitonName, clientIdFile);
            var id = await instance.CreateFolder(Guid.NewGuid().ToString());
            var find = await instance.ShareLinkToggle(id, true);
            ;
            Assert.AreEqual(true, find.IsShared);
            find = await instance.ShareLinkToggle(id, false);
            Assert.AreEqual(false, find.IsShared);
            Assert.AreEqual(true, await instance.DeleteFile(id));
        }

        [TestMethod]
        public async Task GetFolderItems()
        {
            var instance = await
                GoogleDrive.GoogleDrive.Factory(applicaitonName, clientIdFile);
            var fileName = Guid.NewGuid().ToString();
            var id = await instance.CreateFolder(fileName);
            var subID1 = await instance.CreateFolder(Guid.NewGuid().ToString(), id);
            var subID2 = await instance.CreateFolder(Guid.NewGuid().ToString(), id);
            var files = await instance.GetFilesInFolder(id);
            Assert.AreEqual(2, files.Count);
            Assert.IsTrue(files.Exists(e=>e.Id == subID1));
            Assert.IsTrue(files.Exists(e => e.Id == subID2));
            Assert.AreEqual(true, await instance.DeleteFile(id));
        }
    }
```
