---
- name: Run Maven Project Locally
  hosts: local
  become: yes

  tasks:

    - name: Install Java
      apt:
        name: default-jdk
        state: present
        update_cache: yes

    - name: Install Maven
      apt:
        name: maven
        state: present

    - name: Install Git
      apt:
        name: git
        state: present

    - name: Remove old project
      file:
        path: /opt/demoapp
        state: absent

    - name: Clone GitHub Repository
      git:
        repo: 'https://github.com/GAJIN-S/jenkin-CICD.git'
        dest: /opt/demoapp
        clone: yes
        update: yes

    - name: Compile Maven Project
      command: mvn clean compile
      args:
        chdir: /opt/demoapp

    - name: Package Maven Project
      command: mvn package
      args:
        chdir: /opt/demoapp

    - name: Run Java Application
      command: java -cp target/jenkin-CICD-1.0-SNAPSHOT.jar com.example.App
      args:
        chdir: /opt/demoapp
      register: app_output

    - name: Show Application Output
      debug:
        var: app_output.stdout
