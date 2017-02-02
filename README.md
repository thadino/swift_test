# CRUD

An	//
//  CRUD.swift
//  Keebin_Test
//
//  Created by Steffen Lefort on 26/01/2017.
//  Copyright © 2017 Steffen Lefort. All rights reserved.
//

import Foundation


class CRUD {
    
    

    func login(email: String, password: String, callback: @escaping (_ abe: Dictionary<String, Any>)-> ()) {
        print("login is running")
        var a = [String : String]()
        
        // Now escape anything else that isn't URL-friendly
        
        let urlPath = "http://keebintest-pagh.rhcloud.com/login"
        let url = NSURL(string: urlPath)
        let session = URLSession.shared
        let request = NSMutableURLRequest(url: url as! URL)
        request.addValue("application/json", forHTTPHeaderField: "Content-Type")
        request.httpMethod = "POST"
        
        
        
        let jsonObject: [String: Any] = [
            "email": email,
            "password": password,
            
            
            ]
        
        let valid = JSONSerialization.isValidJSONObject(jsonObject) // true
        print("er valid true?: \(valid)")
        
        
        
        do {
            let jsonData = try JSONSerialization.data(withJSONObject: jsonObject, options: .prettyPrinted)             // do something with data
            // if the call fails, the catch block is executed
            
            request.httpBody = jsonData
            
            let task = session.dataTask(with: request as URLRequest, completionHandler: {data, response, error -> Void in
                print("Task completed \(data)")
                if let httpResponse = response as? HTTPURLResponse {
                    print("response code is: \(httpResponse.statusCode)")
                }
                
                
                //Her laver vi data om til en dictionary
                let s = (try! JSONSerialization.jsonObject(with: data!, options: .mutableContainers)) as! NSDictionary
                
                
                
                
                if (s["accessToken"] as? String) != nil {
                    a = s as! [String : String]
                    print("vi har a og den er ikke tom \(a)")
                    
                    // s is not nil, is a String type, and is now stored in a
                } else {
                    print("intet virker vi er i else")
                    // action was either nil, or not a String type
                }
                
                
                
                
                //Printer keys som er i s
                print("get the keys  \(s.allKeys)")
                
                print()
                
                callback(s as! Dictionary<String, Any>)
                
                
            })
            task.resume()
            // The task is just an object with all these properties set
            // In order to actually make the web request, we need to "resume"
        } catch {
            print("task i userPut fejlede")
        }
        
        
    }
    
    
    
    
    
    
    
    func getAllUsers(accessToken: String, refreshToken: String, callback: @escaping (_ allUsersArray: Array<myUser>)-> ()) {
        print("vi kører getAllRoles med denne refreshToken \(refreshToken)")
        
        let urlPath = "http://keebintest-pagh.rhcloud.com/api/users/allusers/"
        let url = NSURL(string: urlPath)
        let session = URLSession.shared
        let request = NSMutableURLRequest(url: url as! URL)
        request.addValue(accessToken, forHTTPHeaderField: "accessToken")
        request.addValue(refreshToken, forHTTPHeaderField: "refreshToken")
        request.httpMethod = "GET"
        
        var userArray = Array<(myUser)>()
        
        let task = session.dataTask(with: request as URLRequest, completionHandler: {data, response, error -> Void in
            print("Task completed \(data)")
            if let httpResponse = response as? HTTPURLResponse {
                print("response code is: \(httpResponse.statusCode)")
            }
            
//            print al info som en string. ---
            
//                        let responseString = NSString(data: data!, encoding: String.Encoding.utf8.rawValue)
//                        print("responseString = \(responseString!)")
            
            
            //Lav multi-dimentional array
            var json:[[String:Any]] = []
            if let data = data, let jsonResponse = try? JSONSerialization.jsonObject(with: data, options: .mutableContainers) as? [[String:Any]] {
                json = jsonResponse!
            }

            //håndter håndtering af flere users end 1. *********************************************
//            
//            for i in json{
            
            var mu = myUser()
            
            
            
            if json.count > 0 {
                for blog in json {
                    mu = myUser()
                    print("vi kører blog")
                    if let email = blog["email"]! as? String {
                        mu.email = (email)
                    }
                    if let id = blog["id"]! as? Int {
                        mu.id = (id)
                    }
                    if let password = blog["password"]! as? String {
                        mu.password = (password)
                    }
                    if let roleId = blog["roleId"]! as? Int {
                        mu.roleId = (roleId)
                    }
                    if let lastName = blog["lastName"]! as? String {
                        mu.lastName = (lastName)
                    }
                    if let firstName = blog["firstName"]! as? String {
                        mu.firstName = (firstName)
                    }
                    if let birthday = blog["birthday"]! as? String {
                        mu.birthday = (birthday)
                    }
                    if let sex = blog["sex"]! as? String {
                        mu.sex = (sex)
                    }
                    
                  userArray.append(mu)
                }
                
            }
            
            
            print("her er userArray size \(userArray.count)")
            for j in userArray{
                print("her er user email: \(j.email!)")
            }
            

          
            
            //Print inholdet af vores json ovenover.
//            for i in json{
//                print("User:")
//                var mu = myUser()
//                for u in i{
//                    mu
//                    print("her er userinfo: \(u.key) & \(u.value)")
//                   
//                }
//                
//            }
            
            
        })
        task.resume()
        callback(userArray)
    }
    
    

    
    
    func createUser(accessToken: String, refreshToken: String, firstName: String, lastName: String, email: String,
                    role: Int, sex: String, password: String){
        
        print("vi kører createUser med denne refreshToken \(refreshToken)")
        
        let urlPath = "http://keebintest-pagh.rhcloud.com/api/users/user/new"
        let url = NSURL(string: urlPath)
        let session = URLSession.shared
        let request = NSMutableURLRequest(url: url as! URL)
        request.addValue("application/json", forHTTPHeaderField: "Content-Type")
        request.addValue(accessToken, forHTTPHeaderField: "accessToken")
        request.addValue(refreshToken, forHTTPHeaderField: "refreshToken")
        request.httpMethod = "POST"
        
        
        
        
        let jsonObject: [String: Any] = [
            "firstName": firstName,
            "lastName": lastName,
            "email": email,
            "roleId": role,
            "birthday": "2010-09-08",
            "sex": sex,
            "password": password
            
        ]
        
        let valid = JSONSerialization.isValidJSONObject(jsonObject) // true
        print("er valid true?: \(valid)")
        
        
        
        do {
            let jsonData = try JSONSerialization.data(withJSONObject: jsonObject, options: .prettyPrinted)             // do something with data
            // if the call fails, the catch block is executed
            
            request.httpBody = jsonData
            print("her er json Data \(request.httpBody!)")
            
            let task = session.dataTask(with: request as URLRequest, completionHandler: {data, response, error -> Void in
                print("Task completed \(data)")
                if let httpResponse = response as? HTTPURLResponse {
                    print("response code is: \(httpResponse.statusCode)")
                }
                
                
                
                
            })
            task.resume()
        } catch {
            print("task i createUser fejlede")
        }
        
        
        
        
    }
    
    
    
    
    
    func putUser(accessToken: String, refreshToken: String, emailCurrent: String, firstName: String, lastName: String, emailNew: String,
                 role: Int, sex: String, password: String){
        
        print("vi kører putUser med denne accessToken \(accessToken) og refreshToken \(refreshToken)")
        
        let urlPath = "http://keebintest-pagh.rhcloud.com/api/users/user/\(emailCurrent)"
        let url = NSURL(string: urlPath)
        let session = URLSession.shared
        let request = NSMutableURLRequest(url: url as! URL)
        request.addValue("application/json", forHTTPHeaderField: "Content-Type")
        request.addValue(accessToken, forHTTPHeaderField: "accessToken")
        request.addValue(refreshToken, forHTTPHeaderField: "refreshToken")
        request.httpMethod = "PUT"
        
        
        
        let jsonObject: [String: Any] = [
            "firstName": firstName,
            "lastName": lastName,
            "email": emailNew,
            "role": role,
            "birthday": "2010-09-08",
            "sex": sex,
            "password": password
            
        ]
        
        let valid = JSONSerialization.isValidJSONObject(jsonObject) // true
        print("er valid true?: \(valid)")
        
        
        
        do {
            let jsonData = try JSONSerialization.data(withJSONObject: jsonObject, options: .prettyPrinted)             // do something with data
            // if the call fails, the catch block is executed
            
            request.httpBody = jsonData
            print("her er json Data \(request.httpBody!)")
            
            let task = session.dataTask(with: request as URLRequest, completionHandler: {data, response, error -> Void in
                print("Task completed \(data)")
                if let httpResponse = response as? HTTPURLResponse {
                    print("response code is: \(httpResponse.statusCode)")
                }
                
                //            print al info som en string. ---
                let responseString = NSString(data: data!, encoding: String.Encoding.utf8.rawValue)
                print("responseString = \(responseString!)")
                
                
            })
            task.resume()
        } catch {
            print("task i userPut fejlede")
        }
        
        
        
    }
    
    
    
    
    
    func deleteUser(userEmail: String, accessToken: String, refreshToken: String){
        print("vi kører deleteUser med denne accessToken \(accessToken) og refreshToken \(refreshToken)")
        
        let urlPath = "http://keebintest-pagh.rhcloud.com/api/users/user/\(userEmail)"
        let url = NSURL(string: urlPath)
        let session = URLSession.shared
        let request = NSMutableURLRequest(url: url as! URL)
        request.addValue("application/json", forHTTPHeaderField: "Content-Type")
        request.addValue(accessToken, forHTTPHeaderField: "accessToken")
        request.addValue(refreshToken, forHTTPHeaderField: "refreshToken")
        request.httpMethod = "DELETE"
        
        
        
        
        
        let task = session.dataTask(with: request as URLRequest, completionHandler: {data, response, error -> Void in
            print("Task completed \(data)")
            if let httpResponse = response as? HTTPURLResponse {
                print("response code is: \(httpResponse.statusCode)")
            }
        })
        task.resume()
        
        
        
    }
    
    
    
    
    
    
    








}

/////////////////////////////////////// main ting

override func viewDidLoad() { super.viewDidLoad() // Do any additional setup after loading the view, typically

 from a nib. 




Let crud = CRUD() 
activityIndicator.startAnimating() 

crud.login(email: "ab@gmail.com", password: "123"){ abe in // 
crud.putUser(accessToken: abe["accessToken"]! as! String, refreshToken: abe["refreshToken"]! as! String,

emailCurrent: "abc@gmail.com", firstName: "abcd", lastName: "Copenhagen", emailNew: "abc@gmail.com",

 role: 1, sex: "female", password: "123") // crud.createUser(accessToken: abe["accessToken"]! as! String, refreshToken: abe["refreshToken"]! as! String, firstName: "abc", lastName: "b", email: "abc@gmail.com", role: 1, sex: "male", password: "123") // crud.deleteUser(userEmail: "abc@gmail.com", accessToken: abe["accessToken"]! as! String, refreshToken: abe["refreshToken"]! as! String) crud.getAllUsers(accessToken: abe["accessToken"]! as! String, refreshToken: abe["refreshToken"]! as! String){allUsersArray in self.userList.append(contentsOf: allUsersArray) //her f蚌 vi table til at reloade med den nye data. Dette skal ske p�DispatchQueue.main. DispatchQueue.main.async{ self.myTable.reloadData() self.activityIndicator.stopAnimating() print("2 userList \(self.userList)") } } } } 
 
 
 # ViewController
 
 //
//  ViewController.swift
//  Keebin_Test
//
//  Created by Steffen Lefort on 24/01/2017.
//  Copyright © 2017 Steffen Lefort. All rights reserved.
//

import UIKit
import PromiseKit






class ViewController: UIViewController, UITableViewDelegate, UITableViewDataSource {
    
    
    @IBOutlet weak var activityIndicator: UIActivityIndicatorView!
    
    @IBOutlet weak var myTable: UITableView!

    var userList = Array<(myUser)>()
    
    func tableView( _ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return userList.count
    }
    
    func tableView( _ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = UITableViewCell(style: UITableViewCellStyle.default, reuseIdentifier: "td")
        cell.textLabel?.text = userList[indexPath.row].email
        return cell
    }
    
    
    
    
    override func viewDidLoad() {
        
        
        
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib.
        
        let crud = CRUD()
        
        activityIndicator.startAnimating()
        
        crud.login(email: "ab@gmail.com", password: "123"){ abe in
            
            
            
            //    crud.putUser(accessToken: abe["accessToken"]! as! String, refreshToken: abe["refreshToken"]! as! String, emailCurrent: "abc@gmail.com", firstName: "abcd", lastName: "Copenhagen", emailNew: "abc@gmail.com", role: 1, sex: "female", password: "123")
            
            //            crud.createUser(accessToken: abe["accessToken"]! as! String, refreshToken: abe["refreshToken"]! as! String, firstName: "abc", lastName: "b", email: "abc@gmail.com", role: 1, sex: "male", password: "123")
            
            
            //    crud.deleteUser(userEmail: "abc@gmail.com", accessToken: abe["accessToken"]! as! String, refreshToken: abe["refreshToken"]! as! String)
            
            crud.getAllUsers(accessToken: abe["accessToken"]! as! String, refreshToken: abe["refreshToken"]! as! String){allUsersArray in
                self.userList.append(contentsOf: allUsersArray)

                //her får vi table til at reloade med den nye data. Dette skal ske på DispatchQueue.main.
                DispatchQueue.main.async{
                    self.myTable.reloadData()
                    self.activityIndicator.stopAnimating()
                    print("2 userList \(self.userList)")

                }
                
            }
        }
    }
    


    
    
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    
    
    
    
    
    
    
    
    
    
    
}









//var g = 0
//
//let myArray = ["1","2", "3", "4", "5", "6", "7"]
//let myArray2 = ["8","9", "10", "11", "12", "13", "14"]
//
//func countUp(a: Array<Any>) -> Promise<String>{
//    g += 1
//    var b = "it worked"
//    var c = "\(g) \(b)"
//    for i in a {
//        print("this is myArray index: \(i))")
//    }
//    return Promise(value: c)
//}
//
//countUp(a: myArray).then{ result in print(result)}.then{(countUp(a: myArray2)).then{result2 in print(result2)}}
//
//
 
