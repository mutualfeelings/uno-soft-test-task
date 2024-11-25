nodes = [
    { :hostname => 'cassandra-server', :ip => '192.168.1.197', :memory => '4096', :cpu => 4, :boxname => "gyptazy/ubuntu22.04-arm64" },
    { :hostname => 'cassandra-client', :ip => '192.168.1.198', :memory => '2048', :cpu => 2, :boxname => "gyptazy/ubuntu22.04-arm64" },
]

Vagrant.configure("2") do |config|
    nodes.each do |node|
        config.vm.box_check_update = false
        config.vm.define node[:hostname] do |nodeconfgi|
            nodeconfgi.vm.box = node[:boxname]
            nodeconfgi.vm.hostname = node[:hostname]
            nodeconfgi.vm.network :private_network, ip: node[:ip]
            nodeconfgi.vm.provider "vmware" do |vmw|
                vmw.memory = node[:memory]
                vmw.cpu = node[:cpu]
            end
        end
        end
    end
    
