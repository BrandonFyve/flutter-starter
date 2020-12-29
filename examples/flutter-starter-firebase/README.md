# Flutter starter


To start with this app you need to register your Flutter App to a Firebase project.

You can follow the respective Firebase setup guide for [Android](https://flutter-starter.github.io/docs/firebase-setup-android) and [iOS](https://flutter-starter.github.io/docs/firebase-setup-ios) setup.

These are the Firebase Authentication methods which we have used to authenticate the user in our app:

```dart
import 'package:api_sdk/models/user.dart';
import 'package:firebase_auth/firebase_auth.dart';

class AuthService {
  final FirebaseAuth _auth = FirebaseAuth.instance;
  String errorMessage;

  //Create User Object from Firebase User
  User _userFromFirebaseUser(FirebaseUser user, token) {
    return user != null
        ? User(uid: user.uid, email: user.email, token: token)
        : null;
  }

  //Signin With email and password
  Future signInWithEmailAndPassword(String email, String password) async {
    try {
      AuthResult userCredential = await _auth.signInWithEmailAndPassword(
          email: email, password: password);
      final res = await userCredential.user.getIdToken();
      return _userFromFirebaseUser(userCredential.user, res.token);
    } catch (err) {
      //getMessageFromErrorCode is a custom function which returns error message for a particular error code.
      errorMessage = getMessageFromErrorCode(err.code);
      return Future.error(errorMessage);
    }
  }

  //Register with email and password
  Future registerWithEmailAndPassword(String email, String password) async {
    try {
      AuthResult userCredential = await _auth.createUserWithEmailAndPassword(
          email: email, password: password);
      final res = await userCredential.user.getIdToken();
      return _userFromFirebaseUser(userCredential.user, res.token);
    } catch (err) {
      //getMessageFromErrorCode is a custom function which returns error message for a particular error code.
      errorMessage = getMessageFromErrorCode(err.code);
      return Future.error(errorMessage);
    }
  }

  //Get Current User 
  Future getCurrentUser() async {
    try {
      final FirebaseUser user = await _auth.currentUser();
      final res = await user.getIdToken();
      return _userFromFirebaseUser(user, res.token);
    } catch (err) {
      return null;
    }
  }

  //Signout
  Future<void> signOut() async {
    return await _auth.signOut();
  }

}

```

We have used the following code for firestore collection management: 

```dart
import 'package:cloud_firestore/cloud_firestore.dart';

class FirebaseApi {
  final Firestore _db = Firestore.instance;
  final String path;
  CollectionReference ref;

  FirebaseApi(this.path) {
    ref = _db.collection(path);
  }

  Future<QuerySnapshot> getDataCollection() {
    return ref.getDocuments();
  }

  Stream<QuerySnapshot> streamDataCollection() {
    return ref.snapshots();
  }

  Future<DocumentSnapshot> getDocumentById(String id) {
    return ref.document(id).get();
  }

  Future<void> removeDocument(String id) {
    return ref.document(id).delete();
  }

  Future<DocumentReference> addDocument(Map data) {
    return ref.add(data);
  }

  Future<void> updateDocument(Map data, String id) {
    return ref.document(id).updateData(data);
  }
}

```
The methods defined in above code snippets will be called in **Shared** folder.

The demo for the app look like this:

<img style="float: left;" src="./book_store.gif"  height="500"/>
