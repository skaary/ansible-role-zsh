Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"

  config.vm.provision "ansible" do |ansible|
    ansible.extra_vars = { ansible_ssh_user: 'vagrant', vagrant: true, zsh_user_name: 'vagrant', zsh_user_group: 'vagrant' }
    ansible.become = true
    ansible.playbook = 'tests/vagrant-playbook.yml'
  end
end